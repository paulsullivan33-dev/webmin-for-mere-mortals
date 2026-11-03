# Installing Webmin

Installing Webmin ends with a web server
running on your machine and one account
able to sign in to it. This chapter does
the lab's route — the official release
tarball and its `setup.sh` — and along
the way names the package route it did
not take. The check at the end is a
listening socket tied to the machine
itself, and nothing else.

## The two routes

Webmin's own download page offers:

- **A package for your system** — a
  `.deb` (with an apt repository the
  project maintains) or an `.rpm`. This
  is the route to prefer on a real
  machine: the package manager then owns
  updates, the files land in the standard
  places (`/usr/share/webmin` for the
  program, `/etc/webmin` for the
  configuration), and a service is
  registered to start at boot.
- **The release tarball**, ending in
  `.tar.gz`, with a `setup.sh` inside.
  You choose where the program, the
  configuration, and the logs live, and
  updates are your own job from then on.

The lab took the tarball, deliberately:
everything it installed sits inside one
scratch directory, where it can be
inspected and deleted without touching
the system's own layout. On a machine
you intend to keep, take the package.
The concepts — miniserv, the
configuration directory, the first
account — are identical either way.

## What the lab downloaded

From the project's official GitHub
releases, `webmin-2.670.tar.gz`
(38,959,343 bytes; SHA-256
`617af07a890bea18a83dca5c9d7168de1ad4d5f3076d8a1df3475f123748dc21`
as downloaded by the lab; 23,549 files
inside). The full record — address,
size, hash, and the version of Perl
already on the machine — is transcript
`webmin-lab/transcripts/t00-versions-and-download.txt`.

Two Perl libraries are worth installing
before setup, from the system packages:

```bash
apt-get install -y libnet-ssleay-perl libauthen-pam-perl
```

`Net::SSLeay` is what lets miniserv do
HTTPS — without it, setup offers no SSL
at all — and `Authen::PAM` is what lets
Webmin check system passwords for
accounts set up that way. The lab
installed both first (same transcript),
and setup's closing message lists the
same families of modules as recommended;
with them present, the lab's install
needed nothing else.

## Running setup.sh

Unpack the tarball and run the script
from inside the unpacked tree:

```bash
tar xzf webmin-2.670.tar.gz
cd webmin-2.670
./setup.sh
```

Setup asks, in order: the configuration
directory, the log directory, the path
to Perl, the web server port, the first
login name, its password (twice),
whether to use SSL, and whether to start
at boot time. Every answer can also be
handed to the script as an environment
variable, which is what the lab did so
the run is reproducible word for word
(transcript
`webmin-lab/transcripts/t01-setup.txt`):

```bash
config_dir=/path/to/etc-webmin \
var_dir=/path/to/var-webmin \
port=18094 login=admin password='<a password you invent>' \
ssl=1 atboot=0 perl=/usr/bin/perl ./setup.sh
```

The lab's choices, and why:

- **Its own configuration and log
  directories** inside the scratch area —
  the tarball route's whole point.
- **Port 18094**, not the default 10000,
  to keep the lab's server out of the
  way of anything else; on your machine
  the default is fine and chapter 9 shows
  how to change it later.
- **Login name `admin`** with an invented
  password. This account is created in
  Webmin's own user list; it is *not* a
  system account.
- **SSL on** (`ssl=1`) — with Net::SSLeay
  installed, setup generates a
  self-signed certificate and serves
  HTTPS from the first minute.
- **Boot start off** (`atboot=0`) — the
  lab starts and stops its server by
  hand.

Setup ends by starting the server and
printing the address to visit. Two of
its parting words deserve quoting,
because chapter 12 returns to them: the
install is *"outside the package
manager"* (updates are now your job),
and the certificate *"is not signed by
one of the recognized CAs"* — your
browser will warn about it, and on your
own machine, reached by its own address,
that warning is expected.

## The first fix: tie it to loopback

Setup leaves miniserv listening on
**every** interface. The lab checked
with `ss` before touching anything
(transcript
`webmin-lab/transcripts/t02-miniserv-conf-as-installed.txt`
for the configuration, and
`t03-bind-and-start.txt` for the
sockets):

```text
LISTEN 0 0 0.0.0.0:18094 0.0.0.0:* users:(("miniserv.pl",...))
```

On a machine with a network cable in it,
that line offers your control panel to
the whole network. One line in
`miniserv.conf` narrows it. Stop the
server, add the line, start again:

```bash
<config-dir>/stop
echo 'bind=127.0.0.1' >> <config-dir>/miniserv.conf
<config-dir>/start
```

The start and stop scripts live in the
configuration directory itself. After
the restart, the same check shows the
only listener the lab ever wanted:

```text
LISTEN 0 0 127.0.0.1:18094 0.0.0.0:* users:(("miniserv.pl",...))
```

One wrinkle the lab met and chapter 13
keeps: miniserv binds a few seconds
*after* the start script returns, while
it loads its core module — an `ss` run
too early shows nothing at all. Wait,
then check.

If your Webmin must serve other machines
— a home server administered from your
desk — bind it to that machine's LAN
address instead of 127.0.0.1, and let
the firewall rules of chapter 8's
discussion narrow it further. What it
should not be, on any machine you care
about, is `0.0.0.0` facing a network you
do not control.

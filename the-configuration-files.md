# The Configuration Files

Webmin has no database and no registry:
everything it knows lives in plain files
in two directories, and an administrator
who can read them is never locked out of
understanding — or repairing — the
panel. This chapter tours both
directories in the lab's install
(transcript
`webmin-lab/transcripts/t14-config-tour.txt`),
where the configuration directory is a
folder the setup script created in
scratch space. In a package install the
same tree sits at `/etc/webmin` and the
var directory at `/var/webmin`.

## The configuration directory

One folder per module, plus the server's
own files — 142 entries in the lab:

```text
$ ls <config-dir> | wc -l
142
```

The server's own, the ones this guide
keeps returning to:

- **`miniserv.conf`** — the web server's
  settings, one `name=value` per line.
- **`miniserv.users`** — who may sign
  in, with hashed passwords (chapter 6).
- **`miniserv.pem`** — the HTTPS
  certificate and its private key, in
  one file. Guard it like any key.
- **`webmin.acl`** — the grant ledger:
  one line per Webmin user, listing
  their modules.
- **`config`** — the global settings.
  The lab's holds, among others:

```text
os_type=debian-linux
real_os_type=Ubuntu Linux
theme=authentic-theme
referers_none=1
```

`referers_none=1` is the referer
guardrail of chapter 8 — and the
Security Warning page itself tells
intruders exactly which line to change
to disable it, which is worth a moment's
thought in chapter 8.
- **`start`, `stop`, `uninstall.sh`** —
  the scripts setup generated, in the
  same directory, so the panel carries
  its own controls with it.

## miniserv.conf, the lines that matter

Setup wrote most of this file (the whole
thing is transcript
`webmin-lab/transcripts/t02-miniserv-conf-as-installed.txt`).
Read it as four groups:

```text
port=18094            # where it listens
root=/path/to/webmin-2.670   # the program tree
ssl=1                 # HTTPS on
keyfile=.../miniserv.pem
bind=127.0.0.1        # the line the lab added (chapter 3)

session=1             # cookie sign-ins
userfile=.../miniserv.users
passdelay=1           # slow down failed logins
blockhost_failures=5  # lock out a host after 5 failures
blockhost_time=60     # ...for 60 seconds

log=1                 # access logging on
logfile=.../miniserv.log
errorlog=.../miniserv.error
pidfile=.../miniserv.pid
```

Changing the port, the binding, or the
session behaviour is editing the
matching line and restarting — recipe 1
in chapter 9 works the port change as
the example. There is also a page inside
Webmin for this file (*Webmin
Configuration* → *Webmin Server*), but
the file is the truth the page edits,
and it stays readable when the server
itself will not start, which is when you
need it most.

## Each module's own folder

Every module directory holds that
module's settings in a file named
`config`, and per-user permission files
where they exist. The Users and Groups
module's, first lines:

```text
$ head <config-dir>/useradmin/config
from_files=0
base_uid=1000
home_base=/home
```

`home_base=/home` is why chapter 5's new
account landed where it did, and
`base_uid=1000` is where automatic UIDs
start counting. The pattern generalises:
when a module behaves surprisingly, its
folder in the configuration directory is
the first place to look, and its options
page inside Webmin is the same file with
labels on.

## The var directory: state and receipts

The second directory holds what changes
while Webmin runs:

```text
miniserv.log  miniserv.error  miniserv.pid
webmin.log    sessiondb.dir   sessiondb.pag
blocked       locks           module.infos.cache
```

`miniserv.log` is the web server's
access log and `miniserv.error` its
complaints — the lab read its startup
lines there, including an honest one:
*IPv6 support cannot be enabled without
the Socket6 perl module* (chapter 13).
`blocked` is where locked-out hosts are
remembered.

`webmin.log` is the audit log, and it is
the file this guide most wants you to
know. One line per action, with the
time, the acting Webmin user, the
session, the source address, the module,
and what was done:

```text
[...] admin ... 127.0.0.1 acl acl/save_user.cgi "create" "user" "lookout" ...
[...] lookout ... 127.0.0.1 global record-login.pl "login" "-" "-"
[...] admin ... 127.0.0.1 global record-failed.pl "failed" "-" "wrongpass"
```

Creations, sign-ins, and the lab's
deliberate wrong password — all
recorded, in order, whether or not
anyone was watching. On a real machine
this log, read regularly, is the
difference between administering a
system and merely hoping about one.

# The Moving Parts

Webmin looks like one program — one
address, one sign-in — but four parts do
four different jobs, and most confusion
about it is one part being mistaken for
another. This chapter names them, with
the lab's own installation as the map:
Webmin 2.670, installed from the release
tarball into a scratch directory, with
its configuration in a neighbouring
directory the setup script created.

## miniserv: the web server

Webmin does not need Apache or nginx. It
ships its own small web server,
**miniserv**, written in Perl like the
rest of Webmin. Miniserv listens on the
port (10000 by default; the lab uses
18094), serves the pages over HTTPS,
checks sign-ins, and keeps the sessions.
Its settings live in one file,
`miniserv.conf`, in the configuration
directory. The lines that matter most,
exactly as the lab's install wrote them
(transcript
`webmin-lab/transcripts/t02-miniserv-conf-as-installed.txt`):

```text
port=18094
ssl=1
session=1
passwd_file=/etc/shadow
```

`port` is where it listens, `ssl=1`
turns on HTTPS with a self-signed
certificate, `session=1` selects
cookie-based sign-ins (the kind a
browser uses), and the `passwd_file`
lines are how *system* passwords can be
checked when an account is set to use
them. Chapter 7 walks the rest of the
file; chapter 3 shows the one line setup
does *not* write — `bind=` — and why the
lab added it by hand.

Miniserv is also the bouncer. It delays
failed sign-ins (`passdelay=1`), counts
failures per host and blocks repeat
offenders (`blockhost_failures=5`,
`blockhost_time=60`), and refuses to
serve module pages to requests that
arrive without a believable referer —
chapter 8 shows the warning page it
answers with instead.

## The modules: one program per job

Everything you *do* in Webmin is done by
a **module**: a small Perl program with
its own pages, one per area of the
system. *Users and Groups* edits
accounts, *Scheduled Cron Jobs* edits
crontabs, *Disk and Network Filesystems*
edits mounts, *Software Packages* drives
the package manager, and so on. The
release tarball carries over a hundred —
the lab's account-creation form offered
110 modules to grant (transcript
`webmin-lab/transcripts/t09-acl-form.txt`).

Each module is a directory in the
program tree (`useradmin/`, `cron/`,
`time/`…) and — the part that matters
for chapter 7 — a matching directory in
the configuration tree holding that
module's own settings. A module only
ever touches its own corner of the
system, which is what makes per-module
permissions meaningful: granting the
*System Time* module lets a user set the
clock and nothing else, as chapter 6
proves.

## The configuration directory

Webmin keeps everything it needs to
remember in one **configuration
directory** — `/etc/webmin` in a
package install, a directory of the
lab's choosing in the tarball install.
The lab's tour (chapter 7, transcript
`t14-config-tour.txt`) counted 142
entries there: one folder per module,
plus the server's own files —
`miniserv.conf`, `miniserv.users` (who
may sign in), `miniserv.pem` (the HTTPS
certificate and key), and `webmin.acl`,
the ledger of which Webmin user may use
which modules.

A second directory, the **var
directory**, holds the changing state:
miniserv's own log and error log, the
session database, and `webmin.log` —
the audit log of who did what, which
this guide treats as a first-class
feature rather than a debugging aid.

## Two kinds of users — do not mix them

The distinction the rest of the guide
stands on:

- **System users** are the machine's
  accounts: lines in `/etc/passwd`,
  created and edited by the *Users and
  Groups* module. They can sign in to
  the machine itself (if their account
  allows it) and have nothing to do with
  Webmin's pages.
- **Webmin users** are accounts for the
  panel only. They live in
  `miniserv.users` in the configuration
  directory, are managed by the *Webmin
  Users* module (internally `acl`), and
  each carries a list of granted modules
  in `webmin.acl`. A Webmin user need not
  exist in `/etc/passwd` at all — the
  lab's `admin` and `lookout` do not —
  though an account can also be set to
  check a system password through PAM.

Chapter 5 works the first kind, chapter
6 the second, and the difference between
them is the difference between changing
who can use the machine and changing who
can administer it.

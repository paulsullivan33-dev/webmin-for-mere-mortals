# Glossary

**ACL (access control list)**
In Webmin, the record of which modules a
Webmin user may use. Kept in the
configuration directory's `webmin.acl` —
one line per user — and managed by the
Webmin Users module, itself named `acl`.

**Audit log**
Webmin's record of who did what:
`<var-dir>/webmin.log`. Sign-ins,
failures, creations, deletions, each
with the time, the Webmin user, the
module, and the source address.

**bind**
The network address a server listens on.
`bind=127.0.0.1` in `miniserv.conf`
confines Webmin to the machine itself.

**Configuration directory**
Where Webmin keeps its settings:
`/etc/webmin` in a package install, or
the directory chosen at `setup.sh` time
in a tarball install. One folder per
module, plus the server's own files.

**Cookie**
A small token a web server asks the
browser to keep and return. Webmin's
session cookie is the sign-in; a
separate cookie planted on the login
page must accompany the login form
itself.

**Module**
One of Webmin's small programs, each
responsible for one area of the system —
users, cron, disks, packages — with its
own pages, configuration folder, and
place in the permission lists.

**miniserv**
Webmin's own web server, written in
Perl. It listens on the panel's port,
serves HTTPS, checks sign-ins, and
enforces the referer, delay, and lockout
rules. Its settings are `miniserv.conf`.

**miniserv.users**
The configuration file listing Webmin's
own users and their hashed passwords.
Not to be confused with `/etc/passwd`.

**PAM (Pluggable Authentication Modules)**
Linux's standard machinery for checking
passwords. A Webmin account set to *Unix
authentication* has its password checked
through PAM against the system account
of the same name.

**Passdelay / blockhost**
Miniserv's sign-in defences: a forced
pause after each failed attempt, and a
temporary refusal of any host that fails
too many times in a row.

**Referer**
The header in which a browser says
which page a request came from.
Webmin's module pages refuse requests
whose referer is missing or foreign —
the Security Warning of chapter 8.

**Root**
The all-powerful account on a Unix
machine. Webmin's modules act with
root's authority, which is why a Webmin
sign-in is guarded like one.

**Self-signed certificate**
An HTTPS certificate a server makes for
itself, rather than one issued by a
recognised authority. Encryption still
works; browsers warn because nobody
vouches for the identity. Webmin's
default, and expected on your own
network.

**Session**
One signed-in visit. Miniserv ties it to
a cookie and a record in its session
database in the var directory, and ends
it at logout or after the idle time the
account declares.

**System user**
A real account on the machine: a line in
`/etc/passwd`, a hash in `/etc/shadow`,
managed by Webmin's Users and Groups
module. Distinct from a Webmin user.

**Usermin**
Webmin's companion panel for ordinary
users — webmail, personal cron jobs,
password changes — without
administration rights. Named in this
guide, not run.

**Var directory**
Where Webmin keeps its changing state:
logs, the session database, the PID
file, the list of blocked hosts.
`/var/webmin` in a package install.

**Virtualmin**
A hosting platform built on Webmin —
virtual servers, domains, mail — for
machines that host other people's
websites. A neighbour, not a part, of
Webmin itself.

**Webmin user**
An account for the panel only, kept in
`miniserv.users`, with its module grants
in `webmin.acl`. It need not exist as a
system user at all.

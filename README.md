# Webmin for Mere Mortals

Administering a Linux machine usually
means the command line: user accounts in
one file, the clock in another tool, the
firewall in a third, each with its own
spelling. **Webmin** puts a web page over
all of it. It is a control panel that
runs on the machine itself — one small
web server of its own, and a module for
each part of the system: users, cron
jobs, disks, packages, services, logs.
You sign in with a browser and work the
machine from forms and buttons instead
of memorised commands.

That convenience comes with a weight
this guide states before anything else:
**a Webmin login is root power through a
web page.** Nearly every module can
change the real system — the users
module writes the real `/etc/passwd` —
so the account that can sign in, and the
network address the server listens on,
matter as much as any setting inside it.
Chapter 1 returns to this line, and the
lab is built to respect it: the whole
guide runs on **loopback only**, bound
to 127.0.0.1, with invented accounts
that are created, inspected, and deleted
again before the lab ends.

The lab behind this guide ran the real
thing: **Webmin 2.670**, the official
release tarball from the project's
GitHub releases, installed into a
scratch directory on a small Linux
machine and run on port 18094. Against
it the lab signed in through the real
login form, read the machine's name, OS
and Webmin version back from Webmin's
own pages, created a real system user
through the Users and Groups module and
proved it from outside — the new line in
`/etc/passwd`, the password hash in
`/etc/shadow`, the home directory on
disk — then deleted the account again
the same way. It created a second,
*Webmin-only* user allowed exactly one
module, and proved the fence holds: that
user opened its one module and was
refused, in so many words, at the door
of another. And it collected the
guardrails working as designed: the
Security Warning served when a page is
requested without a referer, the failed
login recorded in Webmin's audit log,
the plain-HTTP request redirected to
HTTPS.

What the lab could not do, it did not
fake. Webmin's modern theme is a
JavaScript application, so the lab drove
the same forms and addresses a browser
sends — with `curl` — rather than
clicking through rendered pages; what
things *look like* is described, not
graded. The `.deb` package route, boot
time starting, Unix (PAM) sign-ins,
two-factor authentication, Usermin and
the cluster modules are taught from the
documentation and labelled as such
wherever they appear. `criteria.md`
keeps the full ledger of what was run
and what was only read.

## What this guide covers

- [**What Webmin is**](what-webmin-is.md) —
  the mental model: one small web server,
  a module per job, real changes to a
  real machine, and the security line
  stated before any procedure
- [**The moving parts**](the-moving-parts.md) —
  miniserv the web server, the modules,
  the configuration directory, and
  Webmin's own users, who are not the
  system's users
- [**Installing Webmin**](installing-webmin.md) —
  the release tarball and its setup
  script, the Perl modules it needs, the
  first account, and tying the server to
  loopback before anything else
- [**Your first login**](your-first-login.md) —
  the login form and its cookie, the
  dashboard's own statement of the
  machine it runs on, and what a wrong
  password gets you
- [**Managing users and groups**](managing-users-and-groups.md) —
  creating a real system user through
  Webmin's own pages, proving the result
  in the system's own files, and deleting
  it cleanly
- [**Webmin users and permissions**](webmin-users-and-permissions.md) —
  Webmin's separate account list, granting
  one user exactly one module, and
  proving the fence from the inside
- [**The configuration files**](the-configuration-files.md) —
  the configuration directory toured: the
  server's own settings, one folder per
  module, the grant ledger, and the logs
- [**Staying safe**](staying-safe.md) —
  the built-in guardrails, each caught
  working: referer checking, login delays
  and lockouts, HTTPS enforcement, and
  the audit log
- [**Recipes**](recipes.md) — move Webmin
  to a different port, make a one-module
  account, retire a throwaway user, and
  read the audit log to see who did what
- [**The neighbours**](the-neighbours.md) —
  Cockpit, Virtualmin, the hosting
  panels, and plain SSH with a text
  editor: what each does that Webmin
  doesn't, and vice versa
- [**Limits**](limits.md) — root power in
  a browser, a tarball install outside
  the package manager, a theme that needs
  JavaScript, uneven modules, and the
  rest, stated plainly
- [**Troubleshooting**](troubleshooting.md) —
  the cookie error, the Security Warning,
  the inactivity-logout complaint, the
  empty socket check, and the traps this
  guide's lab fell into
- [**Glossary**](glossary.md) — the
  terms, collected in one place

## What it doesn't cover

Running Webmin for a whole fleet — its
cluster modules exist, and chapter 11
says where they fit, but this guide's
machine count is one. Web hosting
management (virtual servers, mail
domains, databases for customers) is
Virtualmin's job, built on top of
Webmin, and is pointed at rather than
taught. Hardening a Webmin that faces
the public internet is likewise beyond
a loopback lab: chapter 8 teaches the
built-in guardrails, and the standing
advice is to keep the panel on a private
network or behind a VPN and SSH tunnel,
not to publish it.

## How to use this guide

Read chapter 1 for the model and the
security line, and chapter 2 before
installing anything — knowing which part
holds what turns later confusion into a
named problem. Chapter 3 installs and
immediately ties the server down to
loopback; chapter 4 signs in during the
same sitting. Chapters 5 and 6 are the
proof chapters: a system user made and
unmade, and a Webmin user fenced into
one module, each checked from outside
the page that claimed it. The lab's
transcripts live in
`webmin-lab/transcripts/`, and the
Security Warning page it collected is
`webmin-lab/security-warning.txt`.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Cockpit for Mere Mortals](https://paulsullivan33-dev.github.io/cockpit-for-mere-mortals/)
- [Watching Your System for Mere Mortals](https://paulsullivan33-dev.github.io/watching-your-system-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/webmin-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/webmin-for-mere-mortals/blob/main/LICENSE-CODE).

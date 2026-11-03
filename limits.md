# Limits

What Webmin cannot do, or does with
edges you should know about before you
rely on it. Each item here was met by
the lab or is stated plainly from the
project's own material.

## The power is the risk

There is no read-only mode for the panel
as a whole and no undo. Modules change
the real system with root's authority:
a user deleted is deleted (chapter 5), a
firewall rule saved is live. Per-module
grants (chapter 6) and per-module
permission files narrow *who* can act,
and the confirmation pages slow the
irreversible acts down, but nothing
turns Webmin into a safe toy. The honest
mitigation is the one the guide keeps
repeating: few accounts, narrow grants,
small audience for the port, backups of
the system taken by something else.

## A tarball install ages alone

The route this guide's lab took is
explicit about its cost, in setup's own
parting words: installed *outside the
package manager*. No `apt upgrade`
will ever offer you a new Webmin; you
must notice releases yourself and re-run
setup to upgrade. The package route
exists precisely to remove this limit —
on a machine you keep, take it. A
stale Webmin is worse than an old one:
it is a privileged web application with
published vulnerabilities accumulating
against it.

## The modern theme needs a browser, really

Webmin's current face — the Authentic
theme — is a JavaScript application.
The lab administered a whole machine
with `curl`, and this guide is the proof
it can be done, but it was done the way
you repair a watch with mittens: every
form read as raw HTML, every field name
hunted down by hand, a 500 error for a
missing cookie, an error for an unset
default. The panel is designed for a
person in a browser. Automation is
better aimed elsewhere — the system's
own commands, or configuration
management (chapter 11) — and Webmin's
own documentation agrees by never
promising a scripting interface.

## Modules are uneven

A hundred-plus modules written over
nearly three decades do not share one
standard of care. The core modules —
users, cron, packages, mounts — are
battle-tested. The long tail includes
modules for services that have faded,
options that modern versions of a
service have renamed, and the occasional
page that assumes a distribution's
layout you do not have. Webmin detects
the operating system (the lab's config
says `os_type=debian-linux`,
`real_os_type=Ubuntu Linux`) and modules
adapt — but when a module and your
machine disagree, the system files are
the referee, which is why this guide
verifies in them so stubbornly.

## One machine, one seat at a time

Webmin administers the machine it runs
on. Cluster modules can fan actions out
to other Webmin installs, and the lab
did not test them; treating a fleet as
fifty separate panels is the practical
limit most people meet first. Sessions,
too, are of their era: one sign-in per
browser profile per user, an idle
logout you must declare (chapter 6's
form refused to proceed without one),
and no concept of the shared, annotated
collaboration a modern console might
offer.

## The installation footprint is Perl-era

Miniserv is its own web server with its
own TLS stack through `Net::SSLeay`;
features quietly degrade when Perl
modules are missing — the lab's error
log noted, without fuss, that IPv6 could
not be enabled without the `Socket6`
module it did not have. The certificate
Webmin generates is self-signed by
design, so the "is this really my
server?" check rests on your first visit
and your network discipline, not on a
certificate authority. And while Webmin
runs almost anywhere Perl does, its
mental home is a conventional Linux
server administered in ordinary ways —
exotic setups get exotic behaviour. (The lab's own sandbox, with
no systemd in charge, still ran it
happily on loopback — but boot-time
starting was deliberately declined at
setup and never tested.)

## What the lab never touched

Usermin (the per-user companion for
mail and personal settings), two-factor
authentication, Unix/PAM sign-ins,
LDAP-backed accounts, and every module
beyond the handful exercised — these
are described in this guide only where
chapters say so, and `criteria.md` keeps
the exact ledger. A limit admitted is a
limit you can plan around.

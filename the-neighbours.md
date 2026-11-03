# The Neighbours

Webmin's job — administering one Linux
machine through something friendlier
than a bare shell — is contested ground.
These are the honest alternatives, each
characterised from its own
documentation; the lab ran none of them,
and says so.

## Cockpit

The closest modern rival, and the one
this series has its own guide to.
Cockpit is the web console from the
Fedora/Red Hat world: services and logs,
storage, networking, accounts, updates,
and a built-in terminal, in a tidy
interface. Its design differs from
Webmin's in three ways that matter.
It works through the system's own
plumbing — systemd, D-Bus, and your
*system* account and sudo rights, so
there is no separate all-powerful user
list to guard. It starts on demand by
socket activation, so nothing listens
until someone knocks. And it is
deliberately narrower: it does the
common jobs well and declines the long
tail of configuration files Webmin will
happily edit. Choose Cockpit for a
modern, single-purpose console on a
systemd distribution; choose Webmin when
you need the breadth — the obscure
service, the hand-editable config, the
module for the thing from 2009.

## Virtualmin

Not a rival — a relative. Virtualmin is
built *on* Webmin and turns the machine
into a web-hosting platform: virtual
servers, domains, mailboxes, databases,
backups, for customers or tenants. If
the machine's job is hosting other
people's websites, Virtualmin is the
tool and Webmin is its foundation;
installing plain Webmin for that job
means assembling by hand what
Virtualmin already integrated. The
guide's lab did not run it; its own
documentation is the next step if
hosting is your actual goal.

## The hosting panels

cPanel, Plesk and their kin sell the
same hosting outcome as a commercial
product — polished, supported, and
priced per account or per server. They
are what Virtualmin competes with, not
what Webmin competes with. A home or
small-office machine administering
itself rarely needs any of the three;
a business selling hosting usually
picks one and pays, reasonably.

## SSH and a text editor

The neighbour that never leaves: a shell
over SSH, the system's own commands, and
the configuration files themselves.
Everything Webmin does, it does by
editing those files and running those
commands — chapter 5's user was a
`useradd` performed by a form. The shell
wins on precision, scriptability, and
being already installed; it loses on
discoverability — nothing at a prompt
lists your options the way a module
index does — and on the safety of
guardrails like Webmin's confirmation
pages and audit log, which the shell
leaves you to build yourself. Most real
administrators use both: the panel for
the occasional and the visual, the shell
for the repeated and the precise. This
guide's habit of verifying every web
action in the system's files is really
the shell's habit, kept.

## Configuration management

For *many* machines, the answer stops
being a panel at all. Ansible and its
kin describe the desired state of a
fleet in files, apply them everywhere,
and make "what changed?" a question
about your repository rather than your
memory. Webmin even ships cluster
modules, and they work — but a panel
whose strength is one machine, browsed
by one person, is the wrong shape for
fifty machines that must agree. Use
Webmin to learn and to tend the
individual; graduate the fleet to
configuration management when repetition
starts to hurt.

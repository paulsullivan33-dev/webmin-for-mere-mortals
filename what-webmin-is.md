# What Webmin Is

A Linux machine keeps its settings in
dozens of places: accounts in
`/etc/passwd`, scheduled jobs in
crontabs, mounts in `/etc/fstab`, and a
different command with different options
for every one of them. **Webmin is a web
control panel for that machine.** You
open a browser, sign in to an address
like `https://your-machine:10000`, and
the machine's administration is laid out
as pages and forms — one section for
users, one for cron, one for disks, one
for software packages — each reading the
real system files and writing real
changes back to them.

Webmin is old, in the good sense: it has
been doing this job since 1997, it is
written in **Perl**, and it runs on
nearly every Unix-like system. It does
not need a separate web server such as
Apache — it brings its own small one,
called **miniserv** — and it does not
replace the system's own configuration
with a database of its own. When you
create a user in Webmin, the user
appears in `/etc/passwd`, because that
is what Webmin edited. This guide proves
that claim rather than asking you to
believe it: chapter 5 makes an account
through the web pages and then reads the
result back from the system's own files,
outside Webmin entirely.

## A first taste

After installing (chapter 3), Webmin is
one address in a browser:

```text
https://127.0.0.1:18094/
```

The lab's install answers there with a
sign-in page whose title is plain about
its job:

```text
<title>Login to Webmin</title>
```

Sign in, and the first thing the
dashboard tells you is what it is
running on — the lab's pages introduced
themselves, in their own page data, as:

```text
Webmin 2.670 (Ubuntu Linux 24.04.5)
```

on a machine named `htch-runtime`.
Chapter 4 reads those facts back in
full.

## The line, stated early

Webmin's modules run with the power of
the **root** account — the account that
can do anything on a Unix machine.
Creating users is the gentle end of it;
other modules edit the firewall, the
boot configuration, and the mail server.
So a Webmin sign-in is not like signing
in to a photo site. **Whoever can sign
in to Webmin effectively owns the
machine**, and whoever can *reach* the
sign-in page gets to try. Three habits
follow, and the whole guide is built on
them:

- **Bind it to a small audience.**
  Webmin's setup leaves the server
  listening on every network interface.
  The lab's first act after installing
  was to tie it to 127.0.0.1 — the
  machine itself — and chapter 3 shows
  the socket before and after.
- **Treat Webmin accounts as keys.**
  Webmin keeps its *own* list of users,
  separate from the system's (chapter
  6), and each can be granted only the
  modules it needs. The lab's second
  account was allowed exactly one.
- **Read the audit log.** Webmin writes
  down what each account did — sign-ins,
  changes, even the failed attempts —
  in its own log (chapter 7). A control
  panel that keeps receipts is safer
  than one that doesn't, provided
  someone reads them.

Chapter 8 collects the guardrails
Webmin itself ships — referer checking,
login delays, host lockouts, HTTPS
enforcement — each demonstrated working
in the lab, including the full text of
the Security Warning it serves when a
request arrives without the right
referer.

## What it is not

Webmin is not a hosting platform: it
administers *one* machine's system, and
the customer-facing business of virtual
servers and mail domains belongs to
Virtualmin, a separate product built on
top of it (chapter 11). It is not a
monitoring system: it shows the
machine's state when you look, and some
modules can check services on a
schedule, but keeping history and
raising alarms is another tool's trade —
the Watching Your System guide in this
series covers the command-line end of
that job. And it is not a sandbox:
nothing about the web pages softens what
the changes do. A deleted user is
deleted.

## Who it is for

The person with one Linux server — at
home, or a small virtual machine
somewhere — who administers it a few
times a month and would rather click a
labelled form than re-look-up the
options of `useradd` each time. The
learner who wants to see the system's
parts laid out with names on them, and
to check each web action against the
files it touched. And the cautious
administrator of a small fleet who keeps
Webmin bound to a private network as a
glass panel over machines they mostly
run by other means.

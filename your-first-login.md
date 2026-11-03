# Your First Login

Signing in is the first thing Webmin
asks of you and — because the panel is
root power — the thing it guards most
carefully. This chapter signs in the way
a browser does, reads back what the
dashboard says about its own machine,
and meets the two responses a wrong
move earns: a flat refusal for a bad
password, and a puzzling error for a
login that skips a step.

The lab drove this chapter with `curl`
rather than a graphical browser — the
same requests, in text, so every step
can be shown exactly. Where a browser
would behave identically, the chapter
says so.

## The login page

Ask for the front page:

```bash
curl -k https://127.0.0.1:18094/
```

Two things happen (transcript
`webmin-lab/transcripts/t04-login-page.txt`).
The `-k` accepts the self-signed
certificate — a browser shows its
warning page here instead, and on your
own machine you confirm the same thing
by clicking through it. And the server
answers 200 with a page titled
**Login to Webmin**, a `Server:
MiniServ` header, and strict transport
and framing headers (HSTS,
`X-Frame-Options: SAMEORIGIN`) — the
guardrails of chapter 8 are already
visible in the response to a stranger.

The response also sets **cookies**. Keep
them: miniserv plants a cookie on the
login page and requires it back with
the form, as an anti-forgery measure.
The lab's first attempt posted the form
cold, without fetching the page first,
and was answered (transcript
`webmin-lab/transcripts/t05-login-and-sysinfo.txt`):

```text
HTTP/1.0 500 Cache issue or no cookies support
```

A browser never trips this, because it
always loads the page before submitting
it — but anyone scripting Webmin meets
it once. Fetch the page, keep the
cookies, then post.

## Signing in

The form posts two fields, `user` and
`pass`, to `session_login.cgi`. With
the lab's account:

```bash
curl -k -c cookies.txt https://127.0.0.1:18094/ -o /dev/null
curl -k -b cookies.txt -c cookies.txt \
     -e https://127.0.0.1:18094/ \
     -d 'user=admin&pass=<the password>' \
     https://127.0.0.1:18094/session_login.cgi
```

(The `-e` sets the referer — chapter 8
explains why Webmin insists on one.)
A correct password earns a `302`
redirect to the front page and a session
cookie in the jar; from then on the jar
*is* the sign-in, exactly as the cookie
in a browser is.

## What the dashboard says

Signed in, ask for `sysinfo.cgi` — the
page behind the dashboard's system
information. The modern theme draws most
of itself with JavaScript, but the page
it serves carries the facts in its own
markup, and the lab read them back
(transcript `t05`):

```text
data-hostname="htch-runtime"
data-title-initial="Webmin 2.670 (Ubuntu Linux 24.04.5)"
data-user="admin"
```

That is the check this chapter promised:
Webmin's own page names the machine it
runs on (`htch-runtime`), the operating
system and its version (Ubuntu Linux
24.04.5, agreeing with the machine's own
`/etc/os-release`), Webmin's version
(2.670), and the account you are signed
in as. Module pages carry the same
statement in their titles — the Users
and Groups module, opened in chapter 5,
titles itself *Users and Groups -
Webmin 2.670 on htch-runtime (Ubuntu
Linux 24.04.5)*. When a control panel
can state the machine's identity in
every page, "am I administering the box
I think I am?" always has an answer on
screen.

## The wrong password

The lab also signed in with a
deliberately wrong password (transcript
`webmin-lab/transcripts/t13-failures.txt`).
The login page comes back with a warning
banner and one sentence:

```text
Login failed. Please try again.
```

No hint about which half was wrong, and
— chapter 8's details — a deliberate
delay before the next attempt is
allowed, a failure count kept per
connecting host, and the attempt written
into Webmin's audit log, where the lab
found it afterwards recorded as its own
line. A control panel's login page is a
door worth knocking on only if the
knocks are slowed, counted, and
remembered; this one does all three.

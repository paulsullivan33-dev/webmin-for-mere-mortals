# Staying Safe

Chapter 1 stated the line: a Webmin
sign-in is root power through a web
page. This chapter is about the fences —
the ones you build by habit (chapter 3's
binding, chapter 6's least-module
grants) and the ones Webmin builds
itself. Each of the built-in ones was
caught working in the lab; none of them
needs to be taken on faith.

## Referer checking: the Security Warning

Every module page Webmin serves checks
where the request *came from* — the
referer. A request that arrives from an
unknown origin, or with no origin at
all, is not served the page. It is
served this (the full text is kept at
`webmin-lab/security-warning.txt`; the
lab collected it by asking for a form
page bare, transcript
`webmin-lab/transcripts/t06-useradmin-index-and-warning.txt`):

> **Warning!** Webmin has detected that
> the program
> `https://127.0.0.1:18094/useradmin/edit_user.cgi`
> was linked to from an unknown URL,
> which appears to be outside the Webmin
> server. This may be an attempt to
> trick your server into executing a
> dangerous command. Make sure your
> browser is configured to send referrer
> information so that it can be verified
> by Webmin.

The attack this defeats is real: a
malicious web page in another browser
tab causing your browser — signed in to
Webmin, cookies and all — to submit a
dangerous form. The referer gives the
request away.

The page goes on to explain how to turn
the check off, naming the configuration
file and the exact line (`referers_none`
in the global `config` — the lab's holds
`referers_none=1`, meaning *block*
unknown referers), and then warns
against its own instructions: disabling
it "has the side effect of opening your
system up to reflected XSS attacks and
so is not recommended". Leave it on.
Scripts driving Webmin, like the lab's,
adapt by sending the referer — every
`curl` in this guide after that first
lesson carries `-e` for exactly this
reason.

## Sign-in defences

Three settings in `miniserv.conf`
(chapter 7) work together at the door:

- **`passdelay=1`** — after a failed
  sign-in, the next attempt is
  deliberately slowed. Guessing gets
  tedious at machine scale, which is the
  point.
- **`blockhost_failures=5` with
  `blockhost_time=60`** — five failures
  from one host and that host is refused
  entirely for a minute; the `blocked`
  file in the var directory remembers.
- **The audit log** — failures are
  recorded with the name tried and the
  address it came from. The lab's single
  wrong password is in `webmin.log` as
  its own line (transcript
  `webmin-lab/transcripts/t13-failures.txt`
  for the attempt itself, `t14` for the
  log).

And the response to a wrong password is
itself a small discipline: *Login
failed. Please try again.* — no hint
whether the name or the password was
wrong, because either hint is half the
answer.

## HTTPS, enforced

Setup with `ssl=1` does more than
encrypt. The lab's server carries
`ssl_enforce=2`, and a plain HTTP
request to its port is not served an
error — it is redirected to the HTTPS
address (transcript `t13`):

```text
< HTTP/1.0 302 Moved Temporarily
< Location: https://127.0.0.1:18094
```

Login pages also set HSTS and
`X-Frame-Options: SAMEORIGIN` (seen in
chapter 4's first response), so a
browser that has visited once will not
quietly downgrade, and the panel cannot
be framed into someone else's page. The
certificate is self-signed — chapter 3
was plain about that — which means the
encryption is real but the *identity*
assurance is only as good as your first
visit's care. On a network you control,
that trade is ordinary; it is one more
reason the audience for the port should
be small.

## The fence you build

None of these replace the two habits
from chapters 3 and 6, because the
built-in fences assume an attacker
*outside* the sign-in page:

1. **Bind narrowly.** `0.0.0.0` offers
   the door to every network the machine
   touches. Loopback, one LAN address, or
   a VPN address are doors offered to
   people you chose.
2. **Grant narrowly.** `webmin.acl`
   should read like a list of jobs, one
   line per person, each as short as
   lookout's.
3. **Read `webmin.log`.** The panel
   keeps receipts for everything,
   including the attempts that failed.
   A log nobody reads is a diary, not a
   defence.

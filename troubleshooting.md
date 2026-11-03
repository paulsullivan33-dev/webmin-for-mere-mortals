# Troubleshooting

Every entry here happened in this
guide's lab, in roughly the order a new
installer meets them. The transcripts
named with each are the evidence.

## The login form answers 500: "Cache issue or no cookies support"

**Symptom.** Posting a correct username
and password to `session_login.cgi`
returns `HTTP 500` and a page titled
*Cache issue or no cookies support*.

**Cause.** Miniserv plants a cookie when
it serves the login page and requires
that cookie back with the submission,
as a forgery check. A script (or a
browser with cookies disabled) that
posts cold fails the check.

**Fix.** Load the login page first, keep
its cookies, then post — in `curl`,
`-c jar` on the GET and `-b jar` on the
POST. Chapter 4 shows the working pair
of commands. (Transcript
`webmin-lab/transcripts/t05-login-and-sysinfo.txt`.)

## A module page is replaced by a Security Warning

**Symptom.** Opening a page inside a
module — the lab asked for the
create-user form — returns a page titled
*Security Warning* instead of the form.

**Cause.** The request carried no
referer Webmin trusts (chapter 8). This
is the anti-forgery fence working, not
a fault: links from outside Webmin, and
bare requests with no origin, are
refused module pages.

**Fix.** Navigate from inside Webmin, as
a browser does; scripts must send a
referer from the panel itself (`curl
-e https://<address>/<module>/`). Do
**not** follow the page's own printed
instructions for disabling the check
(`referers_none=0`) — the same page
warns that doing so opens the system to
reflected XSS attacks. (Transcript
`t06-useradmin-index-and-warning.txt`;
the page's full text is
`webmin-lab/security-warning.txt`.)

## Creating a Webmin user fails: "Missing or non-numeric inactivity logout time"

**Symptom.** *Webmin Users* refuses a
new account that looks fully filled in.

**Cause.** Webmin requires the
inactivity (idle) logout to be declared
— either a number of minutes or the
explicit default — and the lab's
submission left it unset.

**Fix.** Accept the form's proposed
defaults for the restriction fields
unless you deliberately mean otherwise;
an account Webmin cannot fully specify,
it will not half-create. (Transcript
`webmin-lab/transcripts/t10-create-webmin-user.txt`
keeps the failure and the corrected
retry.)

## "Login failed. Please try again." — and then it gets slower

**Symptom.** A correct-looking sign-in
is refused, and retries feel sluggish.

**Cause.** The password (or the name) is
wrong, and `passdelay` is doing its job:
each failure buys a delay, and five
failures from your host buy a lockout
(`blockhost_failures`, chapter 8). The
attempt is in `<var-dir>/webmin.log` as
a `record-failed` line, which says which
name was tried.

**Fix.** Stop guessing. Check the name
against `<config-dir>/miniserv.users`,
and if the password is truly lost,
reset it from the machine itself with
the program tree's password tool —
`<program-dir>/changepass.pl
<config-dir> <user> <new-password>` —
a documented recovery this guide's lab
did not need to perform, and so did not.
(Transcript `t13-failures.txt`.)

## After start, the port shows nothing — briefly

**Symptom.** `ss -tlnp` shows no
listener in the seconds right after the
start script returns, and a test request
gets nothing.

**Cause.** Miniserv loads its core
module before it binds; on a small
machine that is a few seconds.

**Fix.** Wait, then check again. If it
is still absent, read
`<var-dir>/miniserv.error` — the server
logs its startup lines and its reasons
there (the lab's copy is quoted in
`webmin-lab/transcripts/t03-bind-and-start.txt`,
with the successful check in `t04`).

## Plain HTTP to the port gets a redirect, not the panel

**Symptom.** `http://…:18094/` answers
`302` to the same address with
`https://`.

**Cause.** `ssl_enforce` is on. The
panel will not conduct a session —
least of all a sign-in — unencrypted.

**Fix.** Use the `https://` address, and
expect the self-signed certificate
warning in a browser (chapter 3). If
HTTPS itself fails while HTTP redirects
happily, `Net::SSLeay` was missing at
install time: install `libnet-ssleay-perl`
and check `ssl=` in `miniserv.conf`.
(Transcript `t13-failures.txt`.)

## "Selected user no longer exists!"

**Symptom.** Deleting a user a second
time errors instead of succeeding.

**Cause.** The first delete worked.
Webmin's error is its bookkeeping being
exact, and it is the correct response to
a duplicate instruction.

**Fix.** None — verify instead:
`getent passwd <name>` should return
nothing. The lab met this while checking
its own cleanup, which is the healthy
way to meet it. (Transcript
`webmin-lab/transcripts/t16-cleanup-and-stop.txt`.)

## The error log mentions IPv6 and Socket6

**Symptom.** `miniserv.error` says *IPv6
support cannot be enabled without the
Socket6 perl module*, though
`miniserv.conf` asks for `ipv6=1`.

**Cause.** The optional Perl module is
not installed. Webmin notes it and
serves IPv4 only.

**Fix.** If you need IPv6, install the
package (`libsocket6-perl` on
Debian/Ubuntu) and restart. If you do
not, the line is information, not a
fault — the lab left it exactly as it
found it. (Transcript `t03`, via the
same error log.)

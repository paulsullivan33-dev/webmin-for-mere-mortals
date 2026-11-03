# Webmin Users and Permissions

Chapter 5 changed who can use the
machine. This chapter changes who can
*administer* it — Webmin's own accounts,
which live separately from the system's
(chapter 2 drew the line; here it is
worked). The lab creates a second Webmin
user, `lookout`, granted exactly one
module out of more than a hundred, then
signs in as that user and tests the
fence from the inside.

## Where Webmin keeps its users

Two files in the configuration directory
hold the whole story (transcripts
`webmin-lab/transcripts/t09-acl-form.txt`
and `t11-where-grants-live.txt`):

- `miniserv.users` — one line per Webmin
  user: the name, and its password as a
  salted hash. The lab's, after this
  chapter, holds `admin` and `lookout`
  and nothing else.
- `webmin.acl` — one line per user
  listing the modules that user may use.
  The contrast in the lab's file is the
  chapter in miniature:

```text
admin: acl adsl-client apache at backup-config ... (140-odd modules)
lookout: time
```

The managing module is *Webmin Users*
(internally `acl`). Its creation form is
long — password rules, allowed hours,
idle logout, IP restrictions, a theme
choice, and a checklist of every
installed module. Two of its details are
worth knowing before the recipe. The
password field is a choice, not just a
box: *Set to ..* (a password you type),
*Unix authentication* (check the system
password for a same-named account,
through PAM), or *No password accepted*.
And the form insists on its defaults
being declared: the lab's first
submission, which left the inactivity
logout unset, was refused with *Failed
to save user: Missing or non-numeric
inactivity logout time* — Webmin would
rather reject a half-specified account
than guess. Chapter 13 keeps the error.

## Granting one module

The lab's submission named `lookout`,
set an invented password, and ticked
exactly one module, `time` (System
Time). The answer was the usual redirect
— to the Webmin Users index — and the
files above gained their second lines
(transcript
`webmin-lab/transcripts/t10-create-webmin-user.txt`).
Webmin also noted the new user's real
name in the global configuration
(`realname_lookout=` in
the configuration directory's `config`
file), and wrote the creation into the
audit log with every field recorded.

Per-module *detail* permissions live one
level deeper: each module's own
configuration directory can hold a file
per user (`<user>.acl`) saying what that
user may do *inside* the module — which
users they may edit, for instance. The
lab's `lookout` needed none: with no
such file in the `time` directory, the
module's default permissions apply, and
the fence that matters here is the
module list itself.

## Testing the fence

Receipts, not trust (transcript
`webmin-lab/transcripts/t12-lookout-restrictions.txt`).
Signed in as `lookout` — the sign-in
itself works normally, a 302 and a
session — the lab asked for both doors:

```text
$ GET /time/        (the granted module)
HTTP 200
<title>System Time - Webmin 2.670 on htch-runtime (Ubuntu Linux 24.04.5)</title>

$ GET /useradmin/   (not granted)
HTTP 200
<title>Error - ...</title>
Access denied: User lookout is not allowed to use the ...
```

The granted module opens in full. The
ungranted one answers with an error page
naming the user and the refusal. Note
what the fence is and is not: it is a
*module* fence. Inside System Time,
lookout can genuinely set the machine's
clock — one module is real power, sized
to its corner of the system. Choosing
grants is therefore the same discipline
as choosing sudo rules: the smallest set
that does the job, reviewed against
`webmin.acl`, which has the virtue of
being one readable line per person.

Removing a Webmin user is the mirror of
chapter 5's deletion and just as final:
the lab deleted `lookout` from the same
module (the audit log records it), and
`miniserv.users` and `webmin.acl` went
back to a single name each (transcript
`t16-cleanup-and-stop.txt`). The system
never noticed — there was never a system
account to notice.

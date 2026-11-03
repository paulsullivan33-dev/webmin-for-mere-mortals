# Managing Users and Groups

This is the proof chapter. Webmin claims
its forms change the real system; here
one claim is tested end to end. The lab
creates a system user — `labmouse`, an
invented throwaway account — through
the *Users and Groups* module's own
pages, then steps outside Webmin and
reads the result in the system's own
files. Then it deletes the account the
same way and checks the files again.

## The module and its form

Signed in as an account allowed the
module (chapter 6 covers who may),
`/useradmin/` is the module's index: the
existing users and groups, each a link
to its edit page. "Create user" opens
`edit_user.cgi`, a form the lab read
field by field before submitting
anything (transcript
`webmin-lab/transcripts/t07-create-user-form.txt`).
The fields that decide an account:

- `user` — the login name, and `real` —
  the human's name, the fifth field of
  the passwd line.
- `passmode` — how the password is set:
  the form's choices are *Disable
  password authentication*, *Normal
  password* (value 3, with the password
  itself in the `pass` field), and
  *Pre-encrypted password*.
- `uid_def` — the numeric user ID:
  automatic, the next free one, is the
  default.
- `gidmode` — the primary group: an
  existing group (named in `gid`), a new
  group with the user's name, or a new
  group with a name you give.
- `home_base` and `makehome` — where the
  home directory goes and whether to
  create it.
- `shell` — the program the account gets
  at sign-in: `/bin/bash` for a person,
  `/usr/sbin/nologin` for an account that
  should never sign in interactively.

One warning from the lab's own path to
this form: asking for `edit_user.cgi`
without a referer does not get you the
form at all. It gets the Security
Warning of chapter 8. Browsers send
referers inside Webmin naturally; the
check is aimed at links arriving from
outside, and it fires exactly as
designed.

## Creating the account

Submitting the form posts to
`save_user.cgi`. The lab's submission —
name `labmouse`, real name *Lab Mouse*,
a normal password it invented, automatic
UID, existing group `users`, home
directory created under `/home`, shell
`/bin/bash` — was answered with a
redirect back to the user list
(transcript
`webmin-lab/transcripts/t08-create-user.txt`):

```text
HTTP/1.0 302 Moved Temporarily
Location: /useradmin/index.cgi?mode=users
```

Redirects are Webmin's word for "done".
But the chapter's promise was proof from
outside, and outside is unambiguous.
Before the form, the machine had no such
user; after it:

```text
$ getent passwd labmouse
labmouse:x:1000:100:Lab Mouse:/home/labmouse:/bin/bash

$ ls -ld /home/labmouse
drwxr-x--- 1 labmouse users 0 ... /home/labmouse
```

Every field of the form is in that one
passwd line — name, the `x` that points
to the shadow file, UID 1000 (the
automatic choice), group 100 (`users`),
the real name, the home directory, the
shell — and the home directory exists on
disk, owned by the new account. The
shadow file holds a real password hash
(the lab's begins `$y$`, the yescrypt
scheme this system uses), not the plain
password and not a placeholder. The web
form did exactly, and only, what the
command `useradd` would have done.

## Deleting the account

Deletion is the same shape in reverse,
with one extra decision. The user's edit
page offers Delete; the confirmation
page (`delete_user.cgi`, transcript
`webmin-lab/transcripts/t15-delete-user.txt`)
asks the question that matters — delete
the home directory too (`delhome`) —
because removing the account and
removing its files are separate acts,
and Webmin makes you choose the second
on purpose.

The lab chose both, and verified all
three places an account lives
(transcript
`webmin-lab/transcripts/t16-cleanup-and-stop.txt`):

```text
$ getent passwd labmouse
(gone)
$ grep labmouse /etc/passwd /etc/shadow /etc/group
(no trace)
$ ls /home
hatch
```

A second attempt to delete the same
account — the lab's, made while checking
its work — is answered with the tidy
error *Selected user no longer exists!*,
which is the module keeping its own
books straight. And both the creation
and the deletion are in Webmin's audit
log with the acting account, the time,
and the module (`webmin.log`, same
transcript), a habit chapter 7 tours.

Groups work the same way — their own
tab in the module, lines in `/etc/group`
— and everything in this chapter about
accounts was invented for the lab: a
generic name, a generic password,
created and destroyed inside one
sitting, on a machine with nothing
riding on it. On a real machine the
same pages deserve a slower hand for
exactly the reason they are satisfying
here: they work.

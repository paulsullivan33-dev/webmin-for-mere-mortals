# Recipes

Four jobs, each assembled entirely from
steps the earlier chapters ran for real.
Paths below use `<config-dir>` for the
configuration directory — `/etc/webmin`
in a package install, the directory you
chose in a tarball install.

## Recipe 1: Move Webmin to a different port

For when the default 10000 is taken, or
you simply want the panel somewhere less
obvious. The port is one line of
`miniserv.conf` (chapter 7):

```bash
<config-dir>/stop
sed -i 's/^port=.*/port=18094/' <config-dir>/miniserv.conf
<config-dir>/start
sleep 5
ss -tlnp | grep 18094
```

The check is the last line: a listener
on the new port, on the interface you
bound (chapter 3). The `sleep` is not
superstition — miniserv binds a few
seconds after the start script returns,
and checking too early shows an empty
result and a false alarm (chapter 13).
Sign in at the new address and confirm
the page title still names the right
machine before you trust the move.

## Recipe 2: Make a one-module account

A helper — a family member, a junior, a
duty account — who may do exactly one
job through the panel. Worked in full in
chapter 6; the shape:

1. *Webmin Users* → *Create a new Webmin
   user*.
2. Name, a *Set to ..* password, real
   name.
3. Leave the restriction defaults as
   offered — the form refuses an account
   whose inactivity logout is undeclared,
   so accept what it proposes unless you
   mean otherwise.
4. In the module checklist, tick exactly
   the modules the job needs. One is a
   respectable number.
5. Save, then **test the fence
   yourself**: sign in as the new user,
   open the granted module, and open one
   you did not grant. The second must
   answer *Access denied*. A fence you
   have not tested is a hope.

Finish by reading the ledger:
`grep '^<name>:' <config-dir>/webmin.acl`
should print one line with one module.

## Recipe 3: A throwaway account, retired properly

Chapter 5's round trip, as a checklist
for the times you need a temporary
system account — a test, a demo, a
visiting tool:

1. *Users and Groups* → *Create User*:
   automatic UID, a real group, home
   directory created, a shell of
   `/usr/sbin/nologin` unless the account
   truly must sign in.
2. Prove it from outside before handing
   it over: `getent passwd <name>` shows
   the line you intended.
3. When the job ends, delete it from its
   edit page — and on the confirmation
   page, tick **delete home directory**
   unless you have a stated reason to
   keep the files.
4. Prove the retirement too:
   `getent passwd <name>` (nothing),
   `grep <name> /etc/passwd /etc/shadow
   /etc/group` (nothing), `ls /home`
   (no orphan directory).
5. Note the pair of lines in
   `<var-dir>/webmin.log` — creation and
   deletion both recorded — for whoever
   audits later.

## Recipe 4: See who did what

The audit log as a daily instrument
(chapter 7). Three looks:

```bash
# Everything one Webmin user has done
grep ' lookout ' <var-dir>/webmin.log

# Failed sign-ins, ever
grep 'record-failed' <var-dir>/webmin.log

# The most recent dozen actions
tail -12 <var-dir>/webmin.log
```

Each line carries the time, the Webmin
user, the source address, the module,
and the action in plain words —
`"create" "user" "lookout"`, `"login"`,
`"failed"`. On a machine more than one
person administers, this is the answer
to the oldest question in the trade —
*what changed?* — asked of the panel
itself. If the answer ever surprises
you, chapter 8 is the list of fences to
check first.

# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The lab's accounts, names and passwords are invented for
   the lab and destroyed or left meaningless at its end.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 11 names what the tool
   cannot soften: root-equivalent power with no undo, a
   tarball install that receives no package updates, a modern
   theme that is a JavaScript application (drivable by hand
   only with effort), modules of uneven age and care spread
   over a hundred-plus entries, one-machine administration,
   quiet feature loss when optional Perl modules are absent,
   and a self-signed certificate as the identity story.
8. **The ethics rule is in the text, early.** Webmin is a
   credential and authority tool, and chapter 1 and the README
   state its line before any procedure depends on it: a Webmin
   sign-in is root power through a web page, so accounts are
   treated as keys — few of them, narrowly granted, on a port
   bound to the smallest audience that needs it — and the
   audit log is read, not merely kept. The lab bound its
   server to loopback before its first sign-in and created
   its accounts only to fence and then delete them.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

The build machine is a 2-CPU, 7.7 GB Ubuntu 24.04.5 sandbox,
shared with other work while the lab ran. Webmin **2.670**
was installed from the official release tarball
`webmin-2.670.tar.gz`, fetched from the project's GitHub
releases (38,959,343 bytes; SHA-256
617af07a890bea18a83dca5c9d7168de1ad4d5f3076d8a1df3475f123748dc21
as downloaded; 23,549 files), unpacked into a scratch
directory, and installed by its own `setup.sh` with the
configuration and log directories also inside scratch space.
`libnet-ssleay-perl` (1.94) and `libauthen-pam-perl` were
installed from Ubuntu packages first, so HTTPS was available
at setup time. The server ran only on loopback port **18094**
after an explicit `bind=` fix; every account in the lab is an
invention (`admin`, `lookout`, and the system user
`labmouse`), created, used, and deleted within the lab. All
transcripts live in `webmin-lab/transcripts/`; the Security
Warning page's text is kept at
`webmin-lab/security-warning.txt`.

The lab drove Webmin with **curl, not a graphical browser** —
the same forms, cookies, referers and addresses a browser
sends, captured as text. Everything the guide claims about
behaviour was observed in responses, files and logs. What
Webmin's pages *look like* rendered — layout, icons, the
dashboard's visual arrangement — is described from the page
markup and the project's material, not graded, because no
browser rendered them here.

Run for real, with transcripts as evidence:

- Download record, versions, and the two Perl support
  packages installed and verified (`Net::SSLeay` loads and
  reports 1.94) (t00).
- `setup.sh` run with answers supplied as environment
  variables (config/var directories in scratch, port 18094,
  login `admin`, SSL on, boot start off), ending in its
  success message, its installed-outside-the-package-manager
  warning, and its self-signed certificate warning (t01).
- `miniserv.conf` as setup wrote it read in full; version
  file reads 2.670; configuration directory counted at 142
  entries; the as-installed socket listening on `0.0.0.0`
  captured by `ss` (t02).
- The loopback fix: stop, `bind=127.0.0.1` added, start — and
  the empty first `ss`/curl check that raced miniserv's
  delayed bind, kept in the transcript and explained (t03,
  with the settled listener and login page in t04).
- Login page fetched: HTTP 200, `Server: MiniServ`, title
  *Login to Webmin*, security headers (HSTS,
  X-Frame-Options) (t04).
- Sign-in: the cold POST failing with HTTP 500 *Cache issue
  or no cookies support*, then the correct sequence (fetch
  page, keep cookies, POST) returning 302 with a session;
  `sysinfo.cgi`'s page data read back — hostname
  `htch-runtime`, *Webmin 2.670 (Ubuntu Linux 24.04.5)*,
  user `admin` (t05).
- Users and Groups: module index and Create User form read
  field by field; the form first reached via a bare request
  answered by the **Security Warning** page, whose full text
  is preserved in `webmin-lab/security-warning.txt` (t06,
  t07).
- A real system user created through the module
  (`labmouse`: automatic UID 1000, group `users`, home
  created, `/bin/bash`, password set) and proven **outside**
  Webmin: `getent passwd`, the home directory on disk, and a
  yescrypt hash (`$y$…`) in `/etc/shadow` (t08).
- Webmin Users (`acl`): the creation form read (110 modules
  offered); a first submission refused with *Missing or
  non-numeric inactivity logout time*, the corrected one
  creating `lookout` with exactly one module, `time` (t09,
  t10).
- Where the grants live, read directly: `miniserv.users`
  names, `webmin.acl` (`admin:` followed by ~140 modules,
  `lookout: time`), `realname_lookout` in the global config,
  and the creation line in `webmin.log` (t11).
- The fence tested from inside: signed in as `lookout`,
  `/time/` opens (200, *System Time* title) and `/useradmin/`
  answers *Access denied: User lookout is not allowed to
  use…* (t12).
- Failures: wrong password returning *Login failed. Please
  try again.*; plain HTTP to the HTTPS port answered 302 to
  the `https://` address; the failed attempt found recorded
  in `webmin.log` (t13, t14).
- Configuration toured: global `config` (including
  `referers_none=1`), a module's own config
  (`useradmin/config`: `home_base=/home`, `base_uid=1000`),
  the var directory's contents, and the audit log's lines
  for creation, login and failure (t14).
- Cleanup through the modules themselves: `labmouse`
  deleted with its home directory, verified gone from
  `/etc/passwd`, `/etc/shadow`, `/etc/group` and `/home`
  (a duplicate delete is answered *Selected user no longer
  exists!*); `lookout` deleted from Webmin Users, leaving
  `miniserv.users` and `webmin.acl` with `admin` only; both
  deletions present in `webmin.log` (t15, t16). The server
  was then stopped and the port verified free (t16).

Documented only — described from the project's documentation,
not run, and the text does not claim otherwise:

- The `.deb` package and apt-repository route, the RPM
  route, and upgrades by either route or by re-running
  setup.
- Starting Webmin at boot (declined at setup, `atboot=0`)
  and its service integration; `changepass.pl` password
  recovery (named in chapter 12 as the documented remedy).
- Unix (PAM) authentication for Webmin accounts, LDAP,
  two-factor authentication, IP allow-lists and the other
  account restrictions the creation form offers.
- Every module beyond Users and Groups, Webmin Users, the
  dashboard's system information and System Time — including
  cron, packages, mounts, firewall and mail modules — and
  the cluster modules, Usermin and Virtualmin.
- The neighbours of chapter 10 (Cockpit, Virtualmin, the
  commercial panels, configuration management) —
  characterised, not run.
- The rendered look of the Authentic theme in a browser.

Fixtures are this guide's own and deliberately generic: a
hostname the sandbox happened to give itself, three invented
accounts with invented passwords (`labmouse`, `lookout`, and
the setup account `admin`), all created and deleted inside
the lab on a loopback-only server. No real system, network,
or person was administered, scanned, or modelled.

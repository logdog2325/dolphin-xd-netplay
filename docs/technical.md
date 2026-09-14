# OrreLink, technical notes

The detail that used to live in the README. Nothing here is needed to play.

## Playing across different kinds of computer

A room that mixes CPU architectures, Apple Silicon or Android on one side,
Windows or Linux on the other, runs two different recompilers, which split
the game's code into blocks differently and skew Dolphin's clock between the
machines by a few dozen cycles. XD reads that clock to build its GBA link key
and to seed its battle RNG, which is enough to make the same attack miss on
one machine and hit on the other.

In those rooms OrreLink serves XD's clock itself: a value built from the
game's own frame counter plus a per-session number the host picks and syncs,
identical on every machine, while each machine keeps its own recompiler at
full speed. The host's chat says so at Start ("recompilers stay on; OrreLink
serves XD's clock this battle"); the main menu is skipped and dual core is
off for that battle. Rooms that do not mix (Mac ↔ Thor, Windows ↔ Linux) and
solo play are untouched.

`ForceCommonCoreOnMixedArch = True` under `[NetPlay]` in `Dolphin.ini` is an
opt-in "safest" mode that instead runs everyone on the Cached Interpreter , 
correct, but much slower; useful only as an A/B test.

## Checking a match from the logs

Every session log carries proof lines. Comparing the host's and the guest's:

- `t=boot cpu ...`, the core that actually ran (`core_eff`), the
  architecture, and every determinism setting. Everything but `arch`,
  `core_*`, `hw_*` and `rev` must match; a mixed room shows `xd_clock=on`
  with the same `xd_salt`/`xd_seed` on both.
- `t=boot ar ...`, the Action Replay lines that will run. The host's
  `ar synced-send` checksum must equal the guest's `ar synced-recv`, and
  `diff <(grep '^t=boot ar op' host.log) <(grep '^t=boot ar op' guest.log)`
  must be empty.
- `xd ...` during the link and battle, frame counter, logical time base, RNG
  seed and battle-state checksums, keyed by link-command sequence number so
  the two logs line up:
  `diff <(grep ' xd ' host.log | tr -d '\r' | sed -E 's/^t=[0-9]+ //') <(grep ' xd ' guest.log | tr -d '\r' | sed -E 's/^t=[0-9]+ //')`
  is empty for a clean match (Windows logs are CRLF, hence the `tr`); the
  first differing line is the divergence.
- `keys-local ...` and `keys ...`, what each player's own keyboard or
  controller produced for their GBA (`keys-local`, with `gate=0` meaning
  Dolphin was ignoring input because no game window was focused) and what
  the emulated GBA actually received after netplay (`keys`, identical on
  both machines). One line per change, e.g. `keys 001 A` / `keys 000 -`.
  No `keys-local` lines means nothing reached Dolphin; `keys-local` without
  matching `keys` means the press was lost between the machines.
- `session-end`, `stop-request`, `player-left` and `link-silent`, why a
  match ended and who ended it (`kind=peer-lost` after 20 s of silence from
  the other side, `local`/`server` stop requests, a player leaving the room),
  each with the wall-clock time; `netlat` lines carry `wall=` too.
- `link-progress` lines during the GBA connection, negotiating, upload
  percentage, client starting, for each socket in turn. `battlestyle` lines
  record the format, timer and cosmetic picks that were live.

## The GBA link, in numbers

XD does not merely detect the GBAs, it uploads a ~108 KB program to each one
over the emulated link cable at the cable's real speed. Detection finishes
about a second after the last button press; each socket then takes roughly
20 seconds (a 4-second handshake, the upload, and the client booting), and the
second socket starts only after the first has finished. About 45 seconds from
the last menu press to both GBAs linked is normal and fixed.

Once a GBA has linked it is never auto-reset again for that session.

## Why the official BIOS is required

The GameCube performs a handshake before it will talk to a GBA, and on real
hardware the GBA's own BIOS answers it. Nintendo's boot code contains a JoyBus
listener for exactly this, and no clean-room BIOS reimplements it. Tested on
device: the previously bundled open-source BIOS, a current Cult-of-GBA build,
and mGBA's built-in HLE BIOS all fail the same way. The GBA's link window opens
over and over while the handshake never begins. An official dump works every
time, and the launcher checks the file's hash so a wrong file is caught before
that.

## How the builds are made

This repository holds no Dolphin source. CI reconstructs the `xd-netplay`
branch on every run from:

- `dolphin-emu/dolphin` master at a pinned commit
- PR **#14745**, Android dual-screen / integrated-GBA screen and input support
- the `patches/` series, applied in order with `git am`

Then it builds the Android APK, and the macOS, Windows and Linux clients when
those inputs are ticked on a manual run. Because Dolphin checks the git revision
when connecting, all four platforms are built from one run so they can
interoperate.

`configs/` holds reference configuration for the desktop portable layout;
`docs/orre-community-formats.md` is the format reference the rules pins are
built from.

To build: run the **build** workflow from the Actions tab (tick `build_macos`,
`build_windows` and `build_linux` as needed). Artifacts appear on the run page.
Each release keeps the symbol files that turn a crash report's `OrreLink+0x...`
lines into function names, both as a release asset and as a run artifact.

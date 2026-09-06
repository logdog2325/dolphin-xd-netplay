# OrreLink

**Pokémon XD: Gale of Darkness GBA-vs-GBA battles over the internet** — two
players, each on their own emulated Game Boy Advance running Emerald, fighting
in XD's Colosseum with the game's real link-battle engine.

A Dolphin fork with the whole Orre Colosseum workflow built in: pick a format,
paste a Showdown team, host or join, and play. Runs on **Android** (built for
the AYN Thor's dual screens, works on single-screen devices), **macOS** (Apple
Silicon), **Windows** and **Linux**. Every release builds all four from one
commit, so any two players can battle each other.

## What OrreLink adds

**Formats, pinned into the game.** Choose a format in the launcher and XD's
rules screen is set for you — nobody has to page through it by hand, and
nobody can get it wrong:

| Format | Entry | Level | Team rules |
|---|---|---|---|
| **Orre Colosseum** | bring 6, pick 4, doubles | 100 | Restricted ("box") legendaries and Mythicals banned, Soul Dew banned |
| **Orre Unlimited** | bring 6, pick 4, doubles | 100 | everything allowed |
| **Orre Limited** | bring 6, pick 4, doubles | 50 | every legendary banned |
| **Hoenn Stadium** | bring 6, pick 3, singles | 100 | as Orre Colosseum |
| **Hoenn Unlimited** | bring 6, pick 3, singles | 100 | everything allowed |
| **Hoenn Limited** | bring 6, pick 3, singles | 50 | every legendary banned |

Species Clause, Item Clause, Sleep Clause, Freeze Clause and the Self-KO Clause
are on in every format and enforced by the game itself. The team rules are
enforced by OrreLink: a paste that breaks them is flagged in the editor, a host
cannot open a room with an illegal team, and an illegal guest submission is
refused with the reason. **Free** turns all of it off and leaves XD's menus
alone; **OU** applies the community's OU cheat set instead. Rooms show their
format in the lobby name (`[Orre]`, `[Hoenn-L]`, …).

**Team Editor.** Import a Showdown export or a pokepast.es link, set your
in-game name, save. Imports assume maximum happiness unless the paste has a
`Happiness:` line. One button raises every Pokémon to Lv. 100 in level-100
formats (it never lowers one — a Lv. 70 team is legal, a Lv. 50 team leveled
*down* from 100 is not, so that direction does not exist).

**Submit Team.** A joiner hands the host a team from inside the room: paste a
team, or send the party from your own Emerald save, choose your trainer model,
and it is in the host's GBA save before the battle starts.

**Battle Style.** The host picks the battle music — including **no music** —
and the battle location (unused Phenac and Orre colosseum stages, story venues);
XD's own stage row is then pinned blank so it cannot contradict the pick.
Each player picks their own trainer model; the joiner's ride along with the
team submission.

**Clean team preview.** GBA players no longer get XD's default protagonist
mugshot drawn over their side of the team-preview screen.

**Your saves stay yours.** Netplay copies the host's GBA saves to the room at
Start. If you imported a personal Emerald save, what gets sent is a rebuilt
save carrying only your party and trainer identity — never your boxes, items or
story. A guest's team is written into a spare slot and removed when the room
closes, and if a session dies mid-battle the next launch cleans it up before
anything can be played or synced.

**Netplay that gets out of the way.** The input buffer sizes itself from
measured ping. Builds are matched exactly — mismatched versions refuse to
connect, so "it works on mine" never happens. A **Check for Updates** button
tells you when a newer release exists.

**Same launcher everywhere.** Point it at a folder holding the XD disc image,
the Emerald ROM and the GBA BIOS; it configures Dolphin, the two GBAs and the
controls, and takes you to the Team Editor, Battle Style, and Host/Join.

## What you need

| | |
|---|---|
| **Pokémon XD** | Clean **USA** disc image (game ID `GXXE01`) |
| **Pokémon Emerald** | The standard clean English dump (No-Intro "USA, Europe", `BPEE`). **Both players need the same dump.** |
| **Official GBA BIOS** | `gba_bios.bin`, 16 KB. **Required** — see below. |

We can't distribute any of these.

### Why the official BIOS is required

The GameCube performs a handshake before it will talk to a GBA, and on real
hardware the **GBA's own BIOS answers it** — Nintendo's boot code contains a
JoyBus listener for exactly this. No clean-room BIOS reimplements that, so no
replacement works here. Tested on device: the previously bundled open-source
BIOS, a current [Cult-of-GBA](https://github.com/Cult-of-GBA/BIOS) build, and
mGBA's built-in HLE BIOS all fail the same way — the GBA's link window opens
over and over while the handshake never begins.

An official dump works every time. The launcher checks the file's hash, so a
wrong file is caught immediately instead of failing mysteriously.

## Getting started

1. Grab the build for your platform from
   [Releases](https://github.com/logdog2325/dolphin-xd-netplay/releases) — every
   player must be on the **same version**; netplay refuses mismatched builds.
2. **Desktop:** put the XD disc image, the Emerald ROM and the BIOS in one
   folder. Open the app — the **OrreLink launcher** appears on startup and
   configures itself from that folder. **Android:** open the app → **XD Netplay**
   and work the checklist green.
3. Then follow [Playing a match](#playing-a-match) below.

## Playing a match

**The host's saves are the ones that get played.** At Start, netplay copies the
host's GBA saves to everyone in the room. That single fact explains both flows
below, and it is why a guest who builds a team in their own Team Editor and then
joins a room finds their team was not used — their local save was replaced by
the host's. Guests hand their team over instead. It takes one button.

**Format, music and battle location are the host's.** They are synced to the
room with the host's picks; a joiner's launcher settings for those three do
nothing in a room they join. Trainer models are per player.

### Hosting a room

**Set your team before you open the room.** Once it is open your guest slot
locks, and at Start your saves are already on their way to everyone.

1. Pick your **Format** (and any Battle Style picks) in the launcher.
2. Open the **Team Editor** and set the dropdown at the top to
   **Host — GBA port 2**. That is your slot.
3. **Import** a Showdown export or a pokepast.es link, set your in-game name,
   and **Save**. The editor tells you if the team breaks the format's rules.
4. *Optional:* switch to **Guest — GBA port 3** and put a team there too. That
   is the team your opponent plays with if they never submit one — useful for
   testing alone, or for handing a friend a team. A guest who submits simply
   overwrites it.
5. Go to **Netplay** and **Host**. Share the code it gives you. Your in-game
   name can also be set from the room — the **trainer name** field next to the
   chat on desktop, or the **Your trainer name** card on Android.
6. Wait for your opponent to join and submit. A line appears in the room chat
   when their team arrives — if you never see it, they have not sent one and the
   battle will use whatever is in your guest slot.
7. Press **Start**.
8. In XD, go to **VS Mode → GBA vs GBA**. With a format selected the mode and
   rules are already set; confirm through them and start the battle. You drive
   this part: the GameCube controller belongs to the host, so your opponent
   cannot move the cursor even though they are in the session with you.
9. On XD's GBA Connection screen, **touch nothing.** Both GBAs link themselves
   in about **45 seconds** — see [How long the GBA link takes](#how-long-the-gba-link-takes).
   Each player sees and controls only their own GBA.

You will not see a **Submit Team** button while hosting. That is expected — the
host's team comes from the editor, not from a submission.

### Joining a room

**Do not use your own Team Editor for this.** It edits a save that netplay is
about to replace with the host's, which is the single most common source of
"where did my team go".

1. Go to **Netplay**, paste the host's code, and **Join**.
2. Press **Submit Team** — on desktop it is in the room window, on Android it is
   the button in the bottom-right where the host sees **Start**.
3. Fill in **In-game name (max 7)** and either paste a **Showdown export or a
   pokepast.es link**, or tick **Use my save** to send the party from the
   Emerald save you imported on the Host — GBA port 2 slot (only the party and
   your trainer identity travel, nothing else from the save). Pick your trainer
   model. In a level-100 format you can tick **Raise my team to Lv. 100**.
   Press **Send**.
4. Check the room chat for the confirmation line. If it is not there, it did not
   arrive — send it again before the host starts. A team that breaks the room's
   format is refused, and the line says why.
5. Wait for the host to press **Start**. The host then drives XD's menus to
   **VS Mode → GBA vs GBA** — the GameCube controller is theirs, so your buttons
   do nothing here and that is not a bug. Once you reach the GBA Connection
   screen, leave it alone while both GBAs link.

You can submit any time before Start, and re-submitting replaces what you sent.
A name with no Gen 3 equivalent is dropped and the team still goes through, so
check the chat line if the name matters to you.

### How long the GBA link takes

The Connection screen looks like it is doing nothing for a long time. It is
not. XD does not detect the GBAs and move on — it **uploads a small program to
each GBA over the link cable**, one after the other, and that upload runs at
the cable's real speed:

- detection itself finishes about a second after you stop pressing buttons;
- then each socket takes roughly **20 seconds** — a 4-second handshake, the
  ~108 KB upload, and the client booting — and the second socket only starts
  after the first has finished.

So about **45 seconds** from your last menu press to both GBAs linked is normal
and fixed, not lag. Nothing is drawn on screen during it; the session log
records each phase (`link-progress` lines: negotiating, upload percentage,
client starting) for each socket in turn, which is what to send if a link ever
does stall.

- **During that wait, do not press B.** It backs you out of VS mode mid-upload
  and that link can never complete.
- **If nothing has happened for well over a minute**, press B, back out of VS
  mode, go in again, and let it retry. Going back in re-arms the handshake, and
  the retry has worked every time we have hit it.

## Good to know

- **Two devices in the same house usually cannot connect to each other** over
  the traversal server — most routers won't loop a connection back on
  themselves. That is not a bug in the app. Test with someone on a different
  network, or put one device on a phone hotspot.
- **Traversal vs direct**: traversal costs you nothing in latency. The traversal
  server only brokers the introduction and punches through NAT; once connected,
  input goes peer-to-peer and the server never sees a frame of it. Use traversal
  by default — no port forwarding, and you are not handing your IP address to
  whoever joins. Direct connection is worth reaching for in two cases: on a LAN,
  where it is genuinely faster because traffic never leaves the router, and when
  hole-punching fails outright, which some carrier-grade NATs and mobile
  hotspots do. There is no relay fallback, so if traversal cannot punch through,
  direct with UDP **2626** forwarded on the host is the way in — and when the
  network allows neither, see [When the network won't let you
  connect](#when-the-network-wont-let-you-connect).
- **Which GBA slot is yours** in the desktop Controllers window: **GBA 1 when
  you join, GBA 2 when you host** (the host also owns the GameCube controller,
  which takes the first slot). Default keys: D-pad=W/A/S/D, `A`=X, `B`=Z,
  L=Q, R=E, Start=Enter, Select=Backspace. The launcher's *GBA controls* row
  binds these to your keyboard on every GBA slot in one click and applies
  them immediately; the row only reads *mapped* when the bound device really
  exists. If you had the older T/G/F/H keys saved, press it once to switch.
  **Customize…** next to it opens the GBA mapping window directly (Dolphin's
  own Controllers window hides it unless a port is set to GBA) and applies
  your mapping to every slot.
- **macOS**: the app needs **Input Monitoring** permission to read the keyboard
  at all. It asks on first launch; if it was denied, enable *OrreLink* under
  System Settings → Privacy & Security → Input Monitoring and relaunch.
- **Lag**: netplay uses almost no bandwidth, so only latency matters — close
  downloads and uploads during a match. The **buffer sets itself** from
  measured ping, rising quickly when the link degrades and falling slowly so it
  cannot oscillate mid-battle. Leave it alone unless you have a reason not to:
  editing it by hand switches the automatic sizing off for the rest of the
  session. A ping in the hundreds of milliseconds is the network, not the app —
  a freeze or a dropped session on a bad link shows up in the logs as exactly
  that.
- Once a GBA has linked it is never auto-reset again for that session, so team
  preview can take as long as you like.
- **Soul Dew** cannot be banned by XD's own rules screen (its ban list does not
  know the item), so OrreLink's team checks are the ban in the formats that
  have it.

## Playing across different kinds of computer

A room that mixes CPU architectures — Apple Silicon or Android on one side,
Windows or Linux on the other — runs two different recompilers, which split
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
opt-in "safest" mode that instead runs everyone on the Cached Interpreter —
correct, but much slower; useful only as an A/B test.

### Checking a match from the logs

Every session log carries three kinds of proof lines. Comparing the host's
and the guest's:

- `t=boot cpu ...` — the core that actually ran (`core_eff`), the
  architecture, and every determinism setting. Everything but `arch`,
  `core_*`, `hw_*` and `rev` must match; a mixed room shows `xd_clock=on`
  with the same `xd_salt`/`xd_seed` on both.
- `t=boot ar ...` — the Action Replay lines that will run. The host's
  `ar synced-send` checksum must equal the guest's `ar synced-recv`, and
  `diff <(grep '^t=boot ar op' host.log) <(grep '^t=boot ar op' guest.log)`
  must be empty.
- `xd ...` during the link and battle — frame counter, logical time base, RNG
  seed and battle-state checksums, keyed by link-command sequence number so
  the two logs line up:
  `diff <(grep ' xd ' host.log | tr -d '\r' | sed -E 's/^t=[0-9]+ //') <(grep ' xd ' guest.log | tr -d '\r' | sed -E 's/^t=[0-9]+ //')`
  is empty for a clean match (Windows logs are CRLF, hence the `tr`); the
  first differing line is the divergence.

## When the network won't let you connect

Some networks block peer-to-peer traffic no matter which connection type you
pick: apartment-complex and campus wifi, hotel wifi, guest networks, and phone
hotspots trying to host. The tell is **"Could not communicate with host" on
both traversal and direct**, even when both devices are on the same wifi —
managed networks usually run *client isolation*, which stops devices on the
network from talking to each other at all, and they sit behind NAT you cannot
port-forward through. None of that is fixable from inside this app.

**The fix is [Tailscale](https://tailscale.com/download)** — free for personal
use, with apps for Android, macOS, Windows, iOS and Linux, so every platform
OrreLink runs on is covered.

1. Both players install Tailscale and sign into the **same tailnet** (one of
   you invites the other; the app walks you through it).
2. Host in OrreLink: **Battle — Host or Join → Host → Direct connection**.
3. The joiner enters the host's **Tailscale address** — the `100.x.y.z` IP shown
   in the Tailscale app — with port **2626** in the Direct connection fields.

That's the whole trick: OrreLink just dials an IP, and Tailscale carries it
past the building NAT, the missing static IP and the client isolation in one
move. When a network blocks peer-to-peer entirely, Tailscale falls back to its
relay servers and still connects — a little more latency, which the automatic
buffer absorbs.

Two devices in the same room that cannot see each other on the house wifi have
a simpler option too: put both on one phone's hotspot. The phone becomes the
network, no isolation involved, and Direct connection with the **Local**
address from the host's room screen works as-is.

## Reporting problems

Every session writes its own log, and it is far more use than any description of
what went wrong: it records the state of both link sockets once a second, so it
shows exactly where detection stopped. **Send the newest one.**

| | |
|---|---|
| **Android** | Use the **Share Log** button in the app |
| **Windows / macOS / Linux** | Use the **Share Log…** button at the bottom of the launcher: it saves the newest log where you choose and opens that folder |
| **Windows (by hand)** | `%APPDATA%\Dolphin Emulator\GBA\` — press Win+R and paste that (AppData is hidden, which is why browsing to it fails) |
| **macOS (by hand)** | `~/Library/Application Support/Dolphin/GBA/` — in Finder press <kbd>Cmd</kbd>+<kbd>Shift</kbd>+<kbd>G</kbd> and paste it |
| **Linux (by hand)** | `~/.local/share/dolphin-emu/GBA/` |

Files are named `gba_detect_YYYYMMDD_HHMMSS.log`; the two previous sessions are
kept alongside the current one. If a battle failed to start, the log from *that*
session is the one worth sending, not the one after it.

In a netplay session both sides see only half of what happened, so a report is
much stronger with **both players' logs**.

Long-press the bottom GBA screen to switch which of your GBAs it shows. That is
solo only — during netplay it can never show a GBA you do not own.

## Updating

The app has a **Check for Updates** button that tells you whether a newer
release exists and links you to it. Nothing downloads or installs on its own.

**Android, one time only:** builds before 1.0.0 were each signed with a
throwaway key, so 1.0.0 will not install over them — uninstall the old app
first, and **back up your teams before you do**, because uninstalling deletes
the app's saved data. From 1.0.0 onward every build is signed with the same key
and updates install straight over the top.

Everyone in a session must be on the **same release**. Netplay compares the
exact build and refuses to connect mismatched ones.

## PBR Online (bonus mode)

Pokémon Battle Revolution over the community Wiimmfi servers. Point the app at a
clean PBR disc of any region — the online patches are applied in memory at boot,
so the disc on disk is never modified. Online play additionally needs a NAND
backup **from your own Wii**; shared or generated NANDs are rejected and banned
by the server. Offline play works without one. Matchmaking happens inside the
game, not in this app.

## How the builds are made

This repository holds no Dolphin source. CI reconstructs the `xd-netplay` branch
on every run from:

- `dolphin-emu/dolphin` master at a pinned commit
- PR **#14745** — Android dual-screen / integrated-GBA screen and input support
- the `patches/` series, applied in order with `git am`

Then it builds the Android APK, and the macOS, Windows and Linux clients when
those inputs are ticked on a manual run. Because Dolphin checks the git revision
when connecting, all four platforms are built from one run so they can
interoperate.

`configs/` holds reference configuration for the desktop portable layout;
`docs/` holds the format reference the rules pins are built from.

To build: run the **build** workflow from the Actions tab (tick `build_macos`,
`build_windows` and `build_linux` as needed). Artifacts appear on the run page.

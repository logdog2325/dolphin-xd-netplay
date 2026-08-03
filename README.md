# XD Netplay

Dolphin builds for **Pokémon XD: Gale of Darkness GBA-vs-GBA netplay battles** —
two players, each with their own emulated Game Boy Advance running Emerald,
battling over the internet.

Runs on **Android** (built for the AYN Thor's dual screens, works on
single-screen devices), **macOS** (Apple Silicon) and **Windows**. All three are
built from the same commit so any two players can battle each other.

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
   folder. Open the app — the **XD Netplay Launcher** appears on startup and
   configures itself from that folder. **Android:** open the app → **XD Netplay**
   and work the checklist green.
3. Build a team in the **Team Editor** (paste a Showdown export or a
   pokepast.es link). You can set your in-game trainer name there too.

   **Hosts: do this before you open the room.** Netplay sends *your* saves to
   everyone at Start, so whatever is in your team slot when you press Start is
   what you play with — there is no fixing it once the battle begins. The guest
   slot is also locked for as long as your room is open, because by then it may
   already hold your opponent's submitted team, and editing it would overwrite
   what they sent and leave a copy of their spread on your disk. Set your team
   first and the whole problem disappears.
4. **Host** and share your code, or paste a code and **Join**. The host presses
   **Start**.
5. Joining someone else's room? Press **Submit Team** before the host starts.
   Your team and trainer name are sent over and used for the battle, so you do
   not need the host to build anything for you and you never send them a save
   file. Skip it and you play whatever team the host has in their guest slot.
6. On XD's GBA Connection screen, **touch nothing** — both GBAs link themselves
   in about 10–20 seconds. Each player sees and controls only their own GBA.

### If a GBA doesn't get detected

Rare, but it happens: one socket links and the other just sits there. It is not
stuck for good, and the timing is the whole trick:

- **While a socket is still connecting, do not press B.** It backs you out of VS
  mode mid-handshake and the link can never complete. Let it work.
- **Once a socket has clearly stalled** — the other one linked and this one has
  shown nothing for 15–20 seconds — press **B** to back out of VS mode, go back
  in, and try again. It has connected on the retry every time we have hit it.

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
  direct with UDP **2626** forwarded on the host is the way in.
- **Your GBA is "GBA 1"** in the desktop Controllers window, even when you are
  the joiner on socket 3. Default keys: `A`=X, `B`=Z, Start=Enter,
  Select=Backspace, D-pad=T/G/F/H, L/R=Q/W. The launcher's *GBA controls* row
  applies these in one click.
- **macOS**: the app needs **Input Monitoring** permission to read the keyboard
  at all. It asks on first launch; if it was denied, enable *DolphinQt* under
  System Settings → Privacy & Security → Input Monitoring and relaunch.
- **Lag**: netplay uses almost no bandwidth, so only latency matters — close
  downloads and uploads during a match. The **buffer now sets itself** from
  measured ping, rising quickly when the link degrades and falling slowly so it
  cannot oscillate mid-battle. Leave it alone unless you have a reason not to:
  editing it by hand switches the automatic sizing off for the rest of the
  session, which is exactly what you want when tuning deliberately and exactly
  what you don't when you were only curious.
- Once a GBA has linked it is never auto-reset again for that session, so team
  preview can take as long as you like.

## Reporting problems

Every session writes its own log, and it is far more use than any description of
what went wrong: it records the state of both link sockets once a second, so it
shows exactly where detection stopped. **Send the newest one.**

| | |
|---|---|
| **Android** | Use the **Share Log** button in the app |
| **Windows** | `%USERPROFILE%\Documents\Dolphin Emulator\GBA\` — paste that into the File Explorer address bar |
| **macOS** | `~/Library/Application Support/Dolphin/GBA/` — in Finder press <kbd>Cmd</kbd>+<kbd>Shift</kbd>+<kbd>G</kbd> and paste it |

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

Then it builds the Android APK, and the macOS and Windows clients when those
inputs are ticked on a manual run. Because Dolphin checks the git revision when
connecting, all three platforms are built from one run so they can interoperate.

`configs/` holds reference configuration for the desktop portable layout.

To build: run the **build** workflow from the Actions tab (tick `build_macos`
and `build_windows` as needed). Artifacts appear on the run page.

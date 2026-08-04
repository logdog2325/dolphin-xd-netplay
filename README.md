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
3. Then follow [Playing a match](#playing-a-match) below.

## Playing a match

**The host's saves are the ones that get played.** At Start, netplay copies the
host's GBA saves to everyone in the room. That single fact explains both flows
below, and it is why a guest who builds a team in their own Team Editor and then
joins a room finds their team was not used — their local save was replaced by
the host's. Guests hand their team over instead. It takes one button.

### Hosting a room

**Set your team before you open the room.** Once it is open your guest slot
locks, and at Start your saves are already on their way to everyone.

1. Open the **Team Editor**.
2. Set the dropdown at the top to **Host — GBA port 2**. That is your slot.
3. **Import** a Showdown export or a pokepast.es link, set your in-game name,
   and **Save**.
4. *Optional:* switch to **Guest — GBA port 3** and put a team there too. That
   is the team your opponent plays with if they never submit one — useful for
   testing alone, or for handing a friend a team. A guest who submits simply
   overwrites it.
5. Go to **Netplay** and **Host**. Share the code it gives you.
6. Wait for your opponent to join and submit. A line appears in the room chat
   when their team arrives — if you never see it, they have not sent one and the
   battle will use whatever is in your guest slot.
7. Press **Start**.
8. In XD, go to **VS Mode → GBA vs GBA**, then set the rules for the battle —
   battle type, level, clauses, whatever format you are playing — and start it.
   You drive this part: the GameCube controller belongs to the host, so your
   opponent cannot move the cursor even though they are in the session with you.
9. On XD's GBA Connection screen, **touch nothing.** Both GBAs link themselves
   in about 10–20 seconds. Each player sees and controls only their own GBA.

You will not see a **Submit Team** button while hosting. That is expected — the
host's team comes from the editor, not from a submission.

### Joining a room

**Do not use your own Team Editor for this.** It edits a save that netplay is
about to replace with the host's, which is the single most common source of
"where did my team go".

1. Go to **Netplay**, paste the host's code, and **Join**.
2. Press **Submit Team** — on desktop it is in the room window, on Android it is
   the button in the bottom-right where the host sees **Start**.
3. Fill in **In-game name (max 7)** and paste a **Showdown export or a
   pokepast.es link**, then press **Send**.
4. Check the room chat for the confirmation line. If it is not there, it did not
   arrive — send it again before the host starts.
5. Wait for the host to press **Start**. The host then drives XD's menus to
   **VS Mode → GBA vs GBA** and sets the rules — the GameCube controller is
   theirs, so your buttons do nothing here and that is not a bug. Once you reach
   the GBA Connection screen, leave it alone while both GBAs link.

You can submit any time before Start, and re-submitting replaces what you sent.
A name with no Gen 3 equivalent is dropped and the team still goes through, so
check the chat line if the name matters to you.

### If a GBA doesn't get detected

Sometimes a socket does not get picked up — one of them, or both. Nothing is
broken and nothing is stuck for good. The timing is the whole trick:

- **While a socket is still connecting, do not press B.** It backs you out of VS
  mode mid-handshake and the link can never complete. Let it work.
- **Once it has clearly stalled — 15–20 seconds with no progress — press B**,
  back out of VS mode, go in again, and let it retry. This is the fix whether
  one socket linked and the other is sitting there, or neither has connected at
  all. It has come up on the retry every time we have hit it.

Going back in re-arms the handshake and re-rolls its timing, which is why the
second attempt succeeds. Retrying is the normal thing to do here, not a sign
something is wrong with your setup.

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

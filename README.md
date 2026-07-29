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
   pokepast.es link).
4. **Host** and share your code, or paste a code and **Join**. The host presses
   **Start**.
5. On XD's GBA Connection screen, **touch nothing** — both GBAs link themselves
   in about 10–20 seconds. Each player sees and controls only their own GBA.

## Good to know

- **Two devices in the same house usually cannot connect to each other** over
  the traversal server — most routers won't loop a connection back on
  themselves. That is not a bug in the app. Test with someone on a different
  network, or put one device on a phone hotspot.
- **Your GBA is "GBA 1"** in the desktop Controllers window, even when you are
  the joiner on socket 3. Default keys: `A`=X, `B`=Z, Start=Enter,
  Select=Backspace, D-pad=T/G/F/H, L/R=Q/W. The launcher's *GBA controls* row
  applies these in one click.
- **macOS**: the app needs **Input Monitoring** permission to read the keyboard
  at all. It asks on first launch; if it was denied, enable *DolphinQt* under
  System Settings → Privacy & Security → Input Monitoring and relaunch.
- **Lag**: netplay uses almost no bandwidth, so only latency matters — close
  downloads and uploads during a match. Same region: the host can lower the
  netplay **Buffer** (default 5). Long distance: **raise** it (8–10) — a buffer
  too low for the round trip causes constant stutter.
- Once a GBA has linked it is never auto-reset again for that session, so team
  preview can take as long as you like.

## Reporting problems

On Android the bottom GBA screen shows a diagnostic line per GBA:

```
GBA3: rom=1 gba=1 loc=1 lk=1 win=45 rst=86 prb=55055 est=1 lck=1 cmd=14
```

`lk` link open now · `win` link windows opened · `rst` GBA reboots · `prb` times
the game probed · `est`/`lck` link state · `cmd` last command. **Include this
line in any GBA-related report** — it says exactly where detection stopped.
Long-press the bottom screen to switch which of your GBAs it shows (solo only;
in netplay it can never show a GBA you don't own).

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

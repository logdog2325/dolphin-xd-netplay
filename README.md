# XD Netplay for Android

Builds of Dolphin for Pokémon XD: Gale of Darkness GBA-vs-GBA netplay battles
on Android handhelds (built for the AYN Thor's dual screens, works on
single-screen devices too), plus a matching Windows desktop client so both
sides of a netplay session run the identical version.

## What's in the build

The `xd-netplay` branch is constructed by CI from:

- `dolphin-emu/dolphin` master (includes the Android netplay UI merged
  2026-06-28 in PR #14647)
- PR #14745 — Android dual-screen / GBA (Integrated) screen and input support
- `patches/0001` — implements the Android netplay GBA callbacks
  (`FindGBARomPath` config-slot lookup and GBA ROM chat notices)
- `patches/0002` — CI trim: arm64-v8a only, no C++ unit tests

Dolphin netplay requires every player to run the same build (the git revision
is checked at connect), which is why the Windows client is built from the very
same commit.

## Builds

Push to `main` (or run the workflow manually) to build the Android APK.
Tick `build_windows` on a manual run to also produce the Windows desktop zip.
Artifacts appear on the workflow run page. The APK is a debug-type build
(native code is still optimized RelWithDebInfo) and installs alongside any
official Dolphin app.

## Setup for XD battles

Same idea as the original XDNetplay package (stock Dolphin + config files):
you need an NTSC Pokémon XD ISO (GXXE01), an English Emerald ROM, a GBA BIOS,
and the XD OU config/save files. See the `configs/` folder (added separately)
and the setup guide.

# XD Netplay config bundles

Two bundles, same brains as the original XDNetplay package (which was stock
Dolphin 2407 plus these data files — nothing else). Every player needs the
`$XD OU Fixes` cheat file installed locally: Dolphin netplay only syncs which
codes are *enabled*, not the code text itself.

## Android (AYN Thor and other devices)

Install the APK from the build artifacts, run it once, then copy into the
app's user folder (`Android/data/org.dolphinemu.dolphinemu.debug/files/`):

1. `shared/GameSettings/GXXE01.ini` → `GameSettings/GXXE01.ini`
2. `shared/gba_bios.bin` → anywhere (e.g. `GBA/gba_bios.bin`), then set it as
   the GBA BIOS in Settings → GBA
3. Your own `EMERALD.gba` → anywhere, then set it as the Port 2/3 ROM in
   Settings → GBA Link (this is also what netplay uses to match the host's ROM)

Your XD ISO goes in your normal Dolphin games folder. In netplay the host's
GBA saves and memory card are synced automatically — as a guest you don't
need any save files.

## Desktop (Windows/macOS/Linux friends)

Drop the matching desktop build from CI artifacts into a folder, then copy
the entire contents of `desktop-portable/` next to the executable
(`portable.txt` keeps everything self-contained). Add your own:

- `GXXE01/` → your NTSC Pokémon XD ISO
- `EMERALD/EMERALD.gba` → your English Emerald ROM

`EMERALD-2.sav` / `EMERALD-3.sav` are the dummy team saves (host's are used
for both players — build teams into them with PKHeX + Auto-Legality Mod, or
on Android use PKHeXMAUI).

## Battle flow (same as original XDNetplay)

Host: right-click XD → Host with Netplay → assign ports (host = pad 1 +
GBA 2, guest = GBA 3) → Start. In XD: VS MODE → GROUP BATTLE → GBA vs GBA →
Single Battle → rules 6v6 → Adopt Rule. Reset host GBA, then guest GBA, press
A on both, host presses Start.

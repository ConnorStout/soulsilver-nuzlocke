# Installing the Nuzlocke patch

## 1. What you need

| | |
|---|---|
| **Your own SoulSilver ROM** | **Pokémon SoulSilver, USA version**, dumped in full (128 MB). Not the GBC *Pokémon Silver*, and not European or Japanese versions. No ROMs are provided here. Dump your own cartridge, for example with a homebrewed 3DS using GodMode9. |
| **The patch** | [`patch/SoulSilver_Nuzlocke.xdelta`](../patch/SoulSilver_Nuzlocke.xdelta) |
| **An emulator** | Delta (iOS), melonDS (Windows/macOS/Linux/Android), DeSmuME, or similar |

### Checksums

Check your original ROM before patching:

| File | Size | CRC32 | SHA-1 |
|---|---|---|---|
| Original SoulSilver (USA) | 134,217,728 bytes | `D8EA6090` | `f8dc38ea20c17541a43b58c5e6d18c1732c7e582` |
| Patched result | 134,217,728 bytes | `E2770887` | |

If your original doesn't match, the patch won't apply. Common causes: a trimmed ROM (smaller than 128 MB), a different region, or an already-patched file.

## 2. Apply the patch

### Option A: Rom Patcher JS (browser, easiest)
1. Open **https://www.marcrobledo.com/RomPatcher.js/**
2. **ROM file:** choose your original SoulSilver `.nds`.
   - Rom Patcher may say *"File is too big. Force calculate checksum"*. Tap it to check the CRC32 against the table above.
3. **Patch file:** choose `SoulSilver_Nuzlocke.xdelta`.
4. Click **Apply patch** and save the result, for example as `SoulSilver_Nuzlocke.nds`.

On iPhone/iPad this works in Safari, but patching a 128 MB file can run out of memory. If it fails, patch on a computer and AirDrop the result.

### Option B: xdelta3 (command line)
```sh
xdelta3 -d -s "Pokemon SoulSilver (USA).nds" SoulSilver_Nuzlocke.xdelta SoulSilver_Nuzlocke.nds
```

### Option C: Delta Patcher (Windows/macOS GUI)
Choose the original ROM and the `.xdelta` file, then click **Apply patch**.

## 3. Set up Delta (iPhone/iPad)

1. **DS system files (one time only):** Settings (gear) → Core Settings → Nintendo DS. Add `bios7.bin`, `bios9.bin` and `firmware.bin` dumped from your own DS or 3DS.
2. **Add the game:** in the library, tap **+** → **Files** → choose the patched `.nds`. Long-press → **Rename** if you like.
3. Open it and start a **New Game**. Professor Oak will explain the rules.

Tips:
- Delta keeps a resume snapshot when you leave a game. The hack autosaves after every battle, so your progress is in the real save either way.
- After using **test mode**, quit the game and reopen it fresh. Don't resume a save state taken during test mode.

## 4. Other emulators

melonDS and DeSmuME load the patched `.nds` directly. Use a **New Game**. Saves from an unmodified SoulSilver will load, but a fresh start is how the challenge is meant to be played.

## Troubleshooting

| Problem | Fix |
|---|---|
| Patch fails / checksum mismatch | Your ROM isn't the clean USA SoulSilver dump, or it's trimmed. Compare with the checksums above. |
| Game goes straight to the intro with no CONTINUE | You're on a new emulator entry with no save, or the emulator is resuming an old snapshot. Delete the save states and reopen. |
| Imported save doesn't show up in Delta | Make sure the file ends in `.sav` and is fully downloaded (no cloud icon in Files), then long-press the game → Import Save File. |
| Stuck on the RUN OVER screen | That's the end of the run. Start a New Game to try again. |

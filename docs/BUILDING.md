# Building from source

The hack is a set of changes to **[pret/pokeheartgold](https://github.com/pret/pokeheartgold)**, a C decompilation of HeartGold/SoulSilver that rebuilds the game from source.

## 1. Get the decompilation at the right commit

```sh
git clone https://github.com/pret/pokeheartgold.git
cd pokeheartgold
git checkout 6c06ab85516b581bde45f8a804d8cf75ee3e434e
```

The changes were made against commit `6c06ab8` (8 Nov 2026). Later commits may need small fixes when applying.

Follow pokeheartgold's own `INSTALL.md` to set up the toolchain: `arm-none-eabi-gcc`, `make`, `ninja`, `bison`, `flex`, `pkg-config`, `libpng` and Wine on Linux/macOS. Check it builds a matching ROM:

```sh
make soulsilver   # → build/soulsilver.us/pokesoulsilver.us.nds
```

## 2. Apply the Nuzlocke changes

```sh
git apply /path/to/soulsilver-nuzlocke/source/nuzlocke_source.diff
make soulsilver
```

The build's checksum test will now report a mismatch. That's expected, because the ROM is modified.

## 3. Make a patch

```sh
xdelta3 -e -9 -S none -s "Pokemon SoulSilver (USA).nds" \
    build/soulsilver.us/pokesoulsilver.us.nds SoulSilver_Nuzlocke.xdelta
```

`-S none` turns off secondary compression, so browser patchers like Rom Patcher JS can read the file.

## Switching rules on or off

All toggles are at the top of `include/nuzlocke.h`:

```c
#define NUZLOCKE_ENCOUNTER_RULE    1
#define NUZLOCKE_SHINY_CLAUSE      1
#define NUZLOCKE_PERMADEATH        1
#define NUZLOCKE_FORCE_NICKNAMES   1
#define NUZLOCKE_WIPEOUT_ENDS      1
#define NUZLOCKE_AUTOSAVE          1
#define NUZLOCKE_NO_SOFT_RESET     1
#define NUZLOCKE_VICTORY_SCREENS   1
#define NUZLOCKE_RECORDS_ON_SELECT 1
#define NUZLOCKE_TEST_MODE         1
#define NUZLOCKE_TEST_HOLD_FRAMES  (5 * 30) // overworld runs at 30 updates/s
```

Set one to `0` and rebuild. Professor Oak's text (`files/msgdata/msg/msg_0219.json`, message `00035`) doesn't change automatically, so edit it to match.

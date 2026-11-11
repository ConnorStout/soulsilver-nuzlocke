# Technical notes

How the hack is put together, for anyone who wants to modify it.

## Files

| File | Purpose |
|---|---|
| `include/nuzlocke.h`, `src/nuzlocke.c` | Rule logic, save data, encounter log, victory detection, test mode |
| `src/nuzlocke_screen.c` | Run over / victory / records / memorial screens and the saving panel |
| `include/nuzlocke_battle.h`, `src/nuzlocke_battle.c` | Battle-side tracking of what knocked out each Pokémon |
| `src/application/pokegear/map/nuzlocke_pgmap.c` | Pokégear map Nuzlocke view |
| `files/msgdata/msg/msg_0203.json` | Screen text (appended messages 5–79) |
| `files/msgdata/msg/msg_0197.json` | Battle messages for blocked balls (1276–1277) |
| `files/msgdata/msg/msg_0219.json` | Professor Oak's rules speech |

## Save data (no save-format changes)

| Data | Where it lives | Why it's free |
|---|---|---|
| Area-used bits, rule state, deaths, victory snapshots (376 bytes) | `SaveMisc.apricorn_trees[32..127]` | The save reserves 128 apricorn tree slots but the game only has 31. The two loops that touched the spare slots are limited to the real 31. |
| Encounter log (120 × 4 bytes) | Text area of the last Wonder Card slot in the Mystery Gift block | Mystery Gift can't be used on emulators. The card header is untouched so the game still sees an empty slot, and the block checksum is updated after each write. |
| Death records (45 × 32 bytes: species, nickname, level, form, place, cause, foe species, trainer ID, move) | Text areas of Wonder Card slots 0 and 1 and the special Wonder Card (15 records each) | Same as the encounter log |
| "Dead" marker on a Pokémon | Unused ribbon bit `MON_DATA_UNUSED_RIBBON_53` | Not shown anywhere in the game |
| "Needs a nickname" marker on a received Pokémon | Unused ribbon bit `MON_DATA_UNUSED_RIBBON_143` | Cleared once it's named |

Rule state, the "active" flag, deaths and victory records are in `NuzlockeSave`. A compile-time assert checks it fits in the free space.

## Hooks

| Game event | Hook | What happens |
|---|---|---|
| Random wild encounter starts | `Task_WildEncounter` / `Task_SafariEncounter` → `Nuzlocke_OnRandomEncounterStart` | Decide if the catch is allowed, mark the area used, log the encounter |
| Ball thrown | `Task_GetPokemon` (CHECK_IF_TRAINER state) and `BattleControllerPlayer` | Deflect like a trainer battle, with custom messages; the ball isn't used up |
| Catch succeeds | `STATE_GET_POKEMON_ASK_FOR_NICKNAME` | Skip the yes/no and go straight to the naming screen |
| Battle ends | `sub_0205239C` (copies the party back) → `Nuzlocke_OnBattleEnd` | Update the log, bury fainted Pokémon, detect a wipeout, detect Lance/Red victories |
| After a battle, before the overworld reloads | `Task_StartEncounter` / `Task_WildEncounter` call `Nuzlocke_RestoreOverworldAfterBattle` instead of `CallTask_RestoreOverworld` | Victory screen (if any), then the saving panel and `SaveGameNormal` if `Nuzlocke_TakeAutosaveRequest()` says the battle mattered, then the overworld. `CallTask_RestoreOverworld` starts loading the field immediately, so the screens must finish first |
| HP reaches 0 in battle | `BtlCmd_UpdateHealthbarValue` and `BtlCmd_UpdateMonData` → `Nuzlocke_OnBattlerHPChanged` | Record the cause of death (see below) |
| Battle script loaded | `ReadBattleScriptFromNarc` / `ov12_0224EBDC` → `Nuzlocke_BattleScriptLoaded` | Track which script is running at each call depth |
| Catch stored | `Task_GetPokemon` store states → `Nuzlocke_OnMonCaught` | Remember the named catch for the saving panel |
| Gift / starter / Egg | `GiveMon`, `Task_ChooseStarter`, `Task_HatchEggInParty` → `Nuzlocke_OnGiftMon` | Bonus log entry; mark gifts as needing a nickname; hatching always opens the naming screen |
| Naming screen OK | `NamingScreen_HandleCharacterInput` | Blank Pokémon names are refused |
| Area sign popup | `FieldSystem_DrawMapNameAnimation` / `PrintMapNameOnIcon` | Encounter status panel in columns 17–31 of the sign window, using BG palette 14 |
| Revive used in battle | `BattleSystem_RecoverStatus` | Revive has no effect while the rules are active |
| PC closed | `sub_020429A0` (scrcmd) → `Nuzlocke_OnPCClosed` | Return withdrawn dead Pokémon to the graveyard |
| Blackout | `Task_Blackout` | Jump to the run-over screen |
| Continue a save | `FieldTask_ContinueGame_Normal` | A finished run opens straight on the run-over screen, before the overworld loads |
| Overworld input | `FieldInput_Process` | A received Pokémon still needing a nickname gets the naming screen; SELECT opens the records; the L/R+SELECT hold starts test mode |
| Every save write | `FlashWriteChunkInternal` (`save.c`) | In test mode the write is skipped and reported as successful |

## Cause of death

Every source of battle damage ends in the `UPDATE_HP` subscript, so the cause is the script that led there. For each script call depth the hack remembers which script was entered (a *jump* into `UPDATE_HP` keeps the caller, so "poison damage → jump to UPDATE_HP" still counts as poison). When a player Pokémon's HP drops to 0, the innermost script with a known meaning decides the cause: move scripts mean an attack (by a foe, its partner, or itself for Explosion/Memento), and subscripts such as `POISON_DAMAGE`, `BURN_DAMAGE`, `WEATHER_CONTINUES`, `RECOIL_*`, `HURT_SELF_IN_CONFUSION`, `HAZARDS_CHECK`, `ROUGH_SKIN`, `DESTINY_BOND` and others map to their own causes. The cause is stored per personality value and turned into a death record when the battle ends.

Overworld poison can't kill in Generation IV (the Pokémon survives at 1 HP), so there's no field-side death to handle.

## Screens

The Nuzlocke screens draw on main BG2 with plain windows. Pokémon party icons are 32×32 4bpp images stored in the same tile order as a 4×4-tile window, so the icon data is copied directly into a window that uses the icon palettes. No sprite engine is needed.

When a screen opens it saves the VRAM bank mapping, `DISPCNT`, `BG2CNT`, the backdrop colour and the visible-plane cache, and restores them when it closes. The overworld reload after a battle relies on that state. The bottom screen is never touched (except on the final run-over screen).

The Pokégear map view puts the icon in a 4×4 window on `SUB_1` at tile `0x200` (free space between the map graphics and the window tiles), with the icon palettes in sub-BG palette slots 12–15.

## Testing notes

The hack was tested headlessly with DeSmuME (py-desmume), including:
- the encounter rule, ball deflection and ball count
- forced nicknames
- permadeath into the graveyard box
- the run-over screen, including reload
- autosave persistence across reboot
- real Champion Lance and Red battles triggering the victory records
- test-mode save protection (save file byte-identical before and after)
- the records pages and the Pokégear map view
- v1.2: cause of death for a wild attack ("Fell to a wild SENTRET") and a trainer's attack ("Fell to Lance's GYARADOS"), the saving panel for a catch and for a lost encounter plus a death, reload after the panel's save (position and data intact), the area status popup (available / used), the deferred naming screen for a received Pokémon and the blank-name block, the memorial and bonus log pages

Not yet exercised in testing: the PC lock on dead Pokémon, the Revive block in battle, and a real shiny encounter.

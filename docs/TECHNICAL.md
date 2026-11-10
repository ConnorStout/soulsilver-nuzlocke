# Technical notes

How the hack is put together, for anyone who wants to modify it.

## Files

| File | Purpose |
|---|---|
| `include/nuzlocke.h`, `src/nuzlocke.c` | Rule logic, save data, encounter log, victory detection, test mode |
| `src/nuzlocke_screen.c` | Run over / victory / records screens |
| `src/application/pokegear/map/nuzlocke_pgmap.c` | Pokégear map Nuzlocke view |
| `files/msgdata/msg/msg_0203.json` | Screen text (appended messages 5–30) |
| `files/msgdata/msg/msg_0197.json` | Battle messages for blocked balls (1276–1277) |
| `files/msgdata/msg/msg_0219.json` | Professor Oak's rules speech |

## Save data (no save-format changes)

| Data | Where it lives | Why it's free |
|---|---|---|
| Area-used bits, rule state, deaths, victory snapshots (376 bytes) | `SaveMisc.apricorn_trees[32..127]` | The save reserves 128 apricorn tree slots but the game only has 31. The two loops that touched the spare slots are limited to the real 31. |
| Encounter log (120 × 4 bytes) | Text area of the last Wonder Card slot in the Mystery Gift block | Mystery Gift can't be used on emulators. The card header is untouched so the game still sees an empty slot, and the block checksum is updated after each write. |
| "Dead" marker on a Pokémon | Unused ribbon bit `MON_DATA_UNUSED_RIBBON_53` | Not shown anywhere in the game |

Rule state, the "active" flag, deaths and victory records are in `NuzlockeSave`. A compile-time assert checks it fits in the free space.

## Hooks

| Game event | Hook | What happens |
|---|---|---|
| Random wild encounter starts | `Task_WildEncounter` / `Task_SafariEncounter` → `Nuzlocke_OnRandomEncounterStart` | Decide if the catch is allowed, mark the area used, log the encounter |
| Ball thrown | `Task_GetPokemon` (CHECK_IF_TRAINER state) and `BattleControllerPlayer` | Deflect like a trainer battle, with custom messages; the ball isn't used up |
| Catch succeeds | `STATE_GET_POKEMON_ASK_FOR_NICKNAME` | Skip the yes/no and go straight to the naming screen |
| Battle ends | `sub_0205239C` (copies the party back) → `Nuzlocke_OnBattleEnd` | Update the log, bury fainted Pokémon, detect a wipeout, detect Lance/Red victories |
| After a battle, overworld restored | `Task_StartEncounter` / `Task_WildEncounter` state 4 | Autosave (`Field_SaveGameNormal`), but only if `Nuzlocke_TakeAutosaveRequest()` says the battle had a death, a catch, an area's first encounter or a Lance/Red win |
| Revive used in battle | `BattleSystem_RecoverStatus` | Revive has no effect while the rules are active |
| PC closed | `sub_020429A0` (scrcmd) → `Nuzlocke_OnPCClosed` | Return withdrawn dead Pokémon to the graveyard |
| Blackout | `Task_Blackout` | Jump to the run-over screen |
| Continue a save | `FieldTask_ContinueGame_Normal` | A finished run opens straight on the run-over screen, before the overworld loads |
| Overworld input | `FieldInput_Process` | SELECT opens the records; the L/R+SELECT hold starts test mode |
| Every save write | `FlashWriteChunkInternal` (`save.c`) | In test mode the write is skipped and reported as successful |

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

Not yet exercised in testing: the PC lock on dead Pokémon, the Revive block in battle, and a real shiny encounter.

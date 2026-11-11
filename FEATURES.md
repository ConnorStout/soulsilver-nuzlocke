# Feature list

The rules switch on the first time you have a **Poké Ball** in your Bag (the "slow start" rule). Before that, including the first rival battle, nothing is enforced.

## Core Nuzlocke rules

### Encounter rule
- Only the **first wild Pokémon in each area** can be caught, and it counts whether you catch it, it flees, you run, or it faints.
- An *area* is one location name on the Town Map: each route, town, cave and so on. All floors of a cave count as one area.
- Covers grass, caves, surfing, fishing, Headbutt trees, Rock Smash and the Safari Zone.
- After your encounter in an area, thrown balls are **dodged** ("The wild Pokémon dodged the Ball! You already had your encounter in this area!") and **the ball isn't used up**.
- The area is locked in by the autosave when that first battle ends.
- Not counted (always catchable, and they don't use up the area): scripted one-time Pokémon (legendaries, Sudowoodo, Snorlax, the Red Gyarados) and roaming Pokémon (Raikou, Entei). They still go in the encounter log as **bonus entries** (gold): "One-time ○" when caught, "One-time ×" when knocked out.
- Gifts such as your starter and eggs are allowed, and are logged as bonus entries too ("Gift ○", "Egg ○").
- **Area status popup:** when you enter an area that has wild Pokémon, a small panel appears next to the place-name sign: **First encounter available!** (green), or **Encounter used** with the Pokémon you met and ○ / ×.
- The Bug-Catching Contest works as normal.

### Shiny clause
- Shiny Pokémon can **always** be caught, and catching one never uses up the area. Shiny catches are logged as bonus entries ("Shiny ○").

### Permadeath
- A Pokémon that's at 0 HP when **any** battle ends is dead.
- It's removed from your party and placed in the last PC box, renamed **GRAVEYARD**. If that box is full, it goes to any free box.
- Dead Pokémon are marked with an invisible flag. You can view them in the PC, but withdrawing one does nothing: it goes straight back when you close the PC.
- **Revive, Max Revive and Revival Herb don't work** in battle.
- The **Fallen** counter on your records tracks every death.
- **Memorial:** every death is recorded with the Pokémon's icon, nickname, species, level, where it died and **what killed it**, for example *Fell to Falkner's PIDGEOTTO*, *Fell to a wild GEODUDE*, *Died to poison*, *Buried by the sandstorm*, *Fell to recoil from DOUBLE-EDGE*, *Fainted using EXPLOSION*, *Hurt itself in confusion*, *Taken by GENGAR's Destiny Bond*. Up to 45 deaths are kept.
- **Poison outside battle can't kill.** In this generation a poisoned Pokémon walking around survives at 1 HP and the poison wears off, so the rule never triggers outside battle. Poison damage *in* battle can kill, and is recorded as "Died to poison".

### Nickname rule
- The nickname screen opens **automatically after every catch**, without asking.
- **Received Pokémon must be named too:** the starter, gifts (Eevee, Dratini, Tyrogue, Game Corner prizes and so on) and hatched Eggs. If you say "No" to the game's "nickname?" question, or the event never asks, the naming screen opens as soon as you're back in control.
- **Blank names aren't accepted:** pressing OK with an empty name just buzzes.

### Wipeout = game over
- If every Pokémon in your party faints, the run ends on a **NUZLOCKE RUN OVER** screen showing your name, badges, fallen count, play time, number caught and your final team with icons.
- The save is locked: loading it always reopens this screen. Use A or ←/→ to page through your encounter log and victories, ready for screenshots.

### No soft resets
- The **L+R+START+SELECT** soft reset is disabled.
- The game **autosaves after any battle that matters**: when a Pokémon died, you caught something, it was an area's first encounter, or you beat Lance or Red.
- While it saves, a **saving panel** shows what just happened: *ABBY (PIDGEY) Lv.4 – Joined your party!*, *Wild SENTRET Lv.3 – Got away. Encounter used.*, or a fallen Pokémon with its cause of death. The banner turns from **SAVING... DON'T TURN OFF** to **GAME SAVED**; it closes by itself (or press A).
- Ordinary battles, like grinding or a trainer fight where nobody faints, don't trigger a save, so there's no delay. Resetting after them can't undo anything that matters.
- Save from the menu as normal before you stop playing.
- Emulator save states can't be blocked by the game, so not using them is on your honor.

### No outside trading
- Enforced naturally on emulators like Delta, which can't do DS wireless, Wi-Fi, Pal Park or the Pokéwalker.

## Extras

### Professor Oak explains the rules
- His opening speech covers every rule before he asks your name.

### Records screen (SELECT in the overworld)
| Page | Shows |
|---|---|
| **NUZLOCKE IN PROGRESS** | Name, badges, fallen, play time, number caught, current team with icons and levels |
| **ENCOUNTER LOG** (one or more pages) | Each area's first encounter: icon, species, **○ caught** (green) or **× failed** (red), route name, plus caught/failed/fallen totals. Bonus entries (gifts, shinies, one-time encounters, Eggs) are shown in gold |
| **IN MEMORIAM** (one or more pages) | Every fallen Pokémon, 4 per page: icon, nickname, species, level, what killed it and where |
| **ELITE FOUR CONQUERED!** | Snapshot of your stats and team when you first beat Champion Lance |
| **RED DEFEATED!** | Snapshot of your stats and team when you first beat Red |

Controls: **A / →** next page (A on the last page closes), **←** back, **B** close.

### Victory screens
- Beating **Champion Lance** or **Red** for the first time shows a gold victory screen with your stats and winning team. Press A to keep playing. After Lance, the Hall of Fame and credits play as normal.
- Each win is saved as a snapshot, so the records keep showing the team you won with even if your party changes later.

### Pokégear map: Nuzlocke view
- On the Pokégear map, press **SELECT** to switch views.
- Point the cursor at a route to see that area's encounter: the Pokémon's icon, its name and **○ Caught** / **× Failed**, or "No encounter here yet."
- Press **SELECT** again for the normal map.

### Test mode (hidden)
- In the overworld, **hold L+SELECT for 5 seconds** to fight Champion Lance, or **R+SELECT** to fight Red.
- Your party is filled up with level-100 Pokémon (Lugia, Dragonite, Tyranitar, Typhlosion, Gengar, Pikachu), and you get Poké Balls if you have none.
- **Your save is protected:** from the moment test mode starts until you restart the game, nothing is written to the save file (autosaves, the Save menu and the Hall of Fame all skip writing). The records screen shows a red **TEST MODE – NOT SAVED** banner.
- Restart the game (on Delta, quit and reopen it, and don't resume a save state) to return to your real run untouched.

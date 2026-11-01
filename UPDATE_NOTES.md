# WolfGamingZA HeliSearch — recovery update

Original author: RELIFE / VirusomanVS (Workshop 3121468488).
WolfGamingZA customization by Skullrazor. Update existing Workshop 3826826172.

## Withdrawn build

Do NOT use WolfGamingZA_HeliSearch-localization-UNSIGNED.zip. After deployment,
the user reported raw localization identifiers in the base-game main menu.
The added root localization tables are the likely cause. Their exact interaction
with DayZ's loader has not been established; archive checks did not validate the
engine's localization loader. Restore the original signed version on both client
and server first to confirm the menu recovers. Restart the client completely.

## Recovery build

WolfGamingZA_HeliSearch-recovery-UNSIGNED.zip contains NO stringtable.csv or
stringtable.xml in the source or PBO. It replaces only the five existing message
literals in ActionSearchCrashBase.c:

- #STR_RLF_Find -> Search Helicopter
- #STR_RLF_Search -> Helicopter Search
- #STR_RLF_SearchAlredy -> Already Searched
- #STR_RLF_Have -> Supplies Found
- #STR_RLF_Empty -> Nothing useful found.

English strings are passed directly to the existing action/notification methods.
No global localization registration is required. Config.c retains the directory
correction: create $profile:WolfGamingZA to match the existing JSON path.
All other scripts, compiled config.bin, sounds, classes, registrations, probabilities
and network behavior are unchanged from the supplied original PBO. Original
credits and WolfGamingZA branding are preserved.

## Checks and limits

Verified locally: input/output PBO checksums, ten entries, absence of localization
tables, all five direct English messages, exact original gameplay source after
reversing only these string substitutions, only two changed script payloads,
unchanged compiled config/sounds/other scripts, repeatable repack and ZIP CRCs.
This is a targeted repack using the original compiled config, NOT a full DayZ
Tools build. DayZ compilation/runtime and BI signature checks remain UNTESTED.
No claim of in-game readiness is made from these structural checks.

Reproduce from this folder with Python 3 (no third-party dependencies):

    python3 tools/rebuild.py /path/to/ORIGINAL.pbo /tmp/WolfGamingZA_HeliSearch.pbo

Use the original uploaded PBO as the base, not either modified build.

## Install and test safely

1. Back up the installed mod and server profiles/WolfGamingZA/HeliSearch.json.
2. Restore the original signed mod to both server and client; restart both fully.
   Confirm normal menus return. If not, collect the client RPT before proceeding.
3. Sign the recovery PBO using your EXISTING WolfGamingZA.biprivatekey on your
   signing machine. The included public .bikey cannot sign it. Never copy the old
   .bisign to a modified PBO. Verify with official BI signature tools.
4. Test recovery on staging client/server BEFORE publishing. Both must use the
   same new PBO and new .bisign. Keep the original config JSON and matching key.
5. Verify main menu/pause menu, Expansion join messages, and other mod text first.
   Then test helicopter action and success/failure/already-searched notifications.
6. Confirm rewards and repeat searches on vanilla and modded CrashBase wrecks.
7. Only after staging passes, update Workshop item 3826826172. Do not create a
   new item. Keep private signing keys outside every uploaded folder.

Only addons, Keys and existing mod/Workshop metadata are runtime deployment
content. Source, tools and these notes need not be uploaded to Workshop.
Remove the previous bad PBO and its stale signature rather than keeping duplicate
PBOs in any active addons directory. Copying extra stringtable files beside the
mod does not fix a bad table already embedded in the old PBO; replace the PBO.

## Unchanged functionality audit

Search is a standing 10-second hold action on CrashBase subclasses. A server-side
in-memory flag marks each completed search, whether successful or failed. Later
attempts show Already Searched. There are no persistence hooks; a newly created
wreck starts unsearched. No crash spawn events or ordinary loot tables are edited.
Rewards spawn at the player's feet. Chance defaults to 0.8 using the existing >=
random comparison; item count defaults to 5, health 0.8–0.9, selection is random
with replacement. Config loads at server startup. Restart after changing JSON.
Existing code does not validate bad classes, empty lists or numerical bounds;
invalid loot can dereference a null item. This recovery does not change that logic.
No COT identifiers or required addon are present in the supplied source. Its
QuickAction overlay still needs comparison with/without COT on staging.

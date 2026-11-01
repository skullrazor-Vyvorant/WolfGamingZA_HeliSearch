# WolfGamingZA HeliSearch localization update

Original mod: RELIFE / VirusomanVS, Workshop 3121468488.
WolfGamingZA customization: Skullrazor. Update existing Workshop item 3826826172.

## Cause and changes

The supplied PBO has ten entries and no stringtable. The supplied source has a
CSV only in source/data; it was excluded from the published PBO. All five script
references use #STR_RLF_* correctly but cannot resolve without a packaged table.
The update packages a native CSV at the addon root plus matching stringtable.xml.
English and Original entries match. Root CSV provides DayZ localization without
relying on XML import support in the packing tool. The stale nested CSV is removed.

Mappings: Find -> Search Helicopter; Search -> Helicopter Search;
SearchAlredy -> Already Searched; Have -> Supplies Found;
Empty -> Nothing useful found. Existing key spelling is preserved.

Config.c now creates $profile:WolfGamingZA rather than $profile:\\RELIFE,
matching $profile:WolfGamingZA/HeliSearch.json. No other script logic changed.
Compiled config.bin, PBO prefix, sounds, registrations, classes and reward logic
are preserved byte for byte. The invalidated old .bisign is excluded.

## Configuration and behavior audit

- Server MissionServer.OnInit loads the configuration singleton. Existing JSON
  is loaded; defaults are saved when absent. Restart the server after editing.
- HeliChance defaults to 0.8; success uses HeliChance >= Math.RandomFloat01().
  Its comparison and boundary behavior are unchanged.
- countOfItems defaults to 5. Rewards select random entries from HeliItems,
  with replacement, and spawn at the player's position, not in the wreck.
- ItemHealthMin/Max default to 0.8/0.9; health is assigned with a random
  inclusive value in that range. The supplied Extra JSON matches these defaults.
- No validation clamps chance, count or health. Empty loot lists or invalid item
  classes can produce a null object which is then dereferenced by SetHealth01.
  Use valid ItemBase classes, a nonempty list, nonnegative count and ordered health
  values between 0 and 1. These pre-existing limitations are not changed here.
- The standing hold action takes 10 seconds and accepts any CrashBase subclass.
  It does not edit crash events or ordinary crash loot tables.
- CanCheckWreck starts false; completion sets it true after success OR failure.
  Later attempts still take 10 seconds, then display Already Searched.
  This flag is shared per wreck on the server, has no persistence hooks and is
  not network-synchronized. A newly instantiated wreck starts unsearched.
- No COT identifiers, QuickAction prefix or COT required addon appear in this
  source. ActionConstructor and PlayerBase register the action normally.
  The exact source of the displayed COT prefix cannot be proved from this ZIP.
  Compare staging runs with and without COT; do not remove action registration.

## Build and validation

Python 3.12 runs tools/rebuild.py with no third-party dependencies:

    python3 tools/rebuild.py /path/to/ORIGINAL.pbo /tmp/WolfGamingZA_HeliSearch.pbo

Use the original PBO from the uploaded ZIP as the base. The helper preserves the
compiled config.bin, prefix and asset payloads; it does not compile Enforce Script.
It checks input/output PBO SHA-1 trailers, reopens the output, checks all five
script keys against CSV/XML and verifies the exact set of changed payloads.
Do not use this helper to rebuild unrelated config.cpp or sound changes.
For a full Addon Builder build, use source as addon contents and explicitly set
prefix WolfGamingZA_HeliSearch; include root stringtable.csv/stringtable.xml,
scripts and sounds. Ensure the tool does not omit localization files again.

Verified here: archive structure/checksum, 12 entries, five matched translations,
unchanged gameplay scripts/config.bin/sounds, deterministic repeated repack.
Not verified here: DayZ script compilation, client localization loading,
server startup/config generation, multiplayer behavior or BI signature validation.
DayZ and official signing tools are not installed in this environment.

## Signing and staging checklist

The included Keys/WolfGamingZA.bikey is public; it cannot create signatures.
On your existing signing machine, use DayZ Tools DSUtils/DSsignFile with your
existing WolfGamingZA.biprivatekey to sign the UPDATED PBO. Keep the private key
on that machine. Verify the signature against the matching public key using
DSCheckSignatures. Do not reuse the old .bisign or upload the unsigned PBO live.

1. Deploy the same new PBO and new signature to staging client/server. Keep the
   matching public key in the server keys directory and enable signature checks.
2. With a backed-up staging profile, confirm WolfGamingZA/HeliSearch.json is
   created on first start and an edited existing JSON is respected on restart.
3. Check Search Helicopter, Helicopter Search, Supplies Found, Nothing useful
   found, Already Searched in-game. Test English and a non-English client.
4. On staging only, use chance 1 and a known single-item list to check count and
   health; chance -1 forces failure under the existing >= comparison. Restore
   your production settings afterward. Do not infer search rewards from normal
   crash loot: rewards spawn at the player's feet.
5. Test repeats by the same player and a second player; test a fresh wreck and
   server restart. Test a vanilla wreck and compatible modded CrashBase wreck.
6. Compare action behavior with/without COT to identify its QuickAction overlay.
7. Update existing Workshop item 3826826172, preserving original attribution.
   Do not create another Workshop item. No Workshop upload was performed here.

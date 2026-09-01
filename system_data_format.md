# Exported system data format

## Folder structure

The system data is exported to a folder named "System".

## System data index

Within the System folder is a file `_system.json` which provides an entry point into the system data. This is a json format file containing the following:

- "SourceVersion": The version of the core rules PDF (taken from the PDF filename).
- "GameSizes": The filename of the data file containing game size specifications.
- "GroupLimits": The filename of the data file containing group and points limits.
- "Admirals": The filename of the data file containing generic Admirals and Admiral abilities.
- "SecondaryObjectives": The filename of the data file containing secondary objectives.
- "ShipRules": The filename of the data file containing ship special rules.
- "WeaponRules": The filename of the data file containing weapon special rules.

Example:

```json
{
    "SourceVersion": "2.3.1",
    "GameSizes": "game_sizes.json",
    "GroupLimits": "group_limits.json",
    "Admirals": "admirals.json",
    "SecondaryObjectives": "secondary_objectives.json",
    "ShipRules": "ship_rules.json",
    "WeaponRules": "weapon_rules.json"
}
```

## Game sizes data file

The game size definitions are contained in a data file in json format.

The file contains a list of dictionaries containing game size definitions. Each dictionary contains "Name" (string), "MinPts" (integer), "MaxPts" (integer, or null for no limit), and "MaxGroups" (integer).

Example:

```json
[
    {"Name": "Clash", "MinPts": 1001, "MaxPts": 2000, "MaxGroups": 20}
]
```

## Group limits data file

Fleet-construction limits that are not expressed as game size rows are contained in a data file in json format.

The file contains a dictionary with two sections:

- "Tonnage": Limits keyed by ship tonnage (Light, Heavy, Colossal — Medium has no such limit).
- "Rule": Limits keyed by ship special rule name (currently Hero and Rare).

Each entry in either section is a dictionary giving exactly one of the following limit forms, plus a "Scope":

- "MaxPointsFrom": A list of tonnages. The limit is a points cap equal to the combined points of ships of those tonnages elsewhere in the fleet.
- "MaxGroups": A fixed maximum number of Groups.
- "MaxGroupsByGameSize": A dictionary mapping game size name (as used in the game sizes data file) to the maximum number of Groups permitted at that game size.

"Scope" is either "Fleet" (the limit applies across the whole fleet) or "Class" (the limit applies separately to each ship class). The numbers alone cannot distinguish these — a `Rare` limit of `1` at Skirmish size permits one Group *per Rare ship class*, while a `Hero` limit of `2` permits two Groups *in total* across all Hero ships — so "Scope" must be read alongside the limit value.

Example:

```json
{
    "Tonnage": {
        "Light": {"MaxPointsFrom": ["Medium", "Heavy"], "Scope": "Fleet"},
        "Colossal": {"MaxGroupsByGameSize": {"Skirmish": 0, "Clash": 1, "Battle": 2, "Reconquest": 3}, "Scope": "Fleet"}
    },
    "Rule": {
        "Hero": {"MaxGroups": 2, "Scope": "Fleet"},
        "Rare": {"MaxGroupsByGameSize": {"Skirmish": 1, "Clash": 2, "Battle": 3, "Reconquest": 4}, "Scope": "Class"}
    }
}
```

## Admirals data file

Details of the generic Admirals and Admiral abilities are contained in a data file in json format.

The file contains a dictionary with keys:

- "Admirals": A list of dictionaries containing generic Admiral details. Each dictionary contains "Level" (integer), "Cost" (integer), and "UnavailableIn" (list of strings giving game sizes that Admiral cannot be used in).
- "Abilities": A list of generic Admiral abilities that may be used by Admirals in any fleet. Each entry consists of a dictionary containing "Cost" (string — see 'Ability cost format'), "Name" (string), and "Effect" (string).

Example:

```json
{
    "Admirals": [
        {"Level": 3, "Cost": 40, "UnavailableIn": ["Skirmish"]}
    ],
    "Abilities": [
        {"Cost": "2AP", "Name": "Contain Reactor", "Effect": "When a player would roll for Explosion, instead of rolling, make the result of an Explosion roll (for you or your opponent) a 2."}
    ]
}
```

### Ability cost format

The `"Cost"` field on ability dictionaries is always a string. The possible forms are:

| Value | Meaning |
|---|---|
| `"NAP"` (e.g. `"2AP"`) | Fixed cost of N points |
| `"NAP*"` (e.g. `"2AP*"`) | Base cost of N points with a conditional modifier described in the effect text |
| `"*AP"` | Variable cost defined in the fleet abilities table (used for abilities whose cost depends on context) |

## Secondary objectives data file

The secondary objectives are contained in a data file in json format.

The file contains a list of secondary objectives. Each secondary objects consists of a dictionary containing "Name" and "Text" keys. Both have string values.

Example:

```json
[
    {"Name": "Key Site", "Text": "Nominate one Dropsite at least 24\" from your Deployment Zone before the game. If you Control it at the end of the game, gain 2 VP. If this Dropsite is within 6\" or inside your opponent's Deployment Zone, gain 3VP instead"},
    {"Name": "Annihilate", "Text": "You are awarded 1VP at the end of the game for every 500 points of Ships and Admirals you have destroyed. A maximum of 3VP may be scored using this Secondary Objective."}
]
```

## Ship and weapon rule data files

The ship and weapon rules are contained in data files in json format.

Each file contains a list of rules. Each rule consists of a dictionary containing "Name" and "Text" keys, along with an optional "Example" key. All have string values.

Example weapon rules data file:

```json
[
    {"Name": "Corruptor-X", "Text": "When this Weapon successfully inflicts damage, place X Battalions on the target."},
    {"Name": "Critical-X", "Text": "Each of this Weapon's criticals increases the damage of that hit by X.", "Example": "A Critical-2 weapon rolls 2 dice to attack, one is a hit and the other is a critical. The hit deals its normal damage while the critical increases the damage it causes by 2."}
]
```

# JSD Mapping | 1ST Update: Special FX

## Introduction

This is a Repository to help people understand JSD's (Jump Showdown's) Compact data they use for skills. By reversing their keys & whatnot I managed to map almost everything. This will be updated as much as possible, and if you're wondering, no JJS doesnt need one of these repos, as their JSON is readable.

Exported skills are Base91 + zlib. After decompress you get JSON with short keys (`i`, `p`, `ab`, `"1"`, `"ht"`, …). This repo maps those keys to the labels in SB.

## Credits

**Me (@endrosity)** - Mapping & .json.

**Axvaud (@axvaud)** - Skill data with everything in it so I could map everything a bit easier.

## Files

| File | Use |
|---|---|
| `mapping.json` | Everything keyed by ingame name. |
| `unmapped-skill-example.json` | An example skill with almost everything in the game, decompressed & unmapped. |
| `mapped-skill-example.json` | The mapped version of the example skill. |

## Graph shape

```json
{
  "v": 0,
  "p": { "1": "Skill Name", "4": 10 },
  "b": [
    {
      "p": { "1": "Regular" },
      "c": [],
      "b": [
        { "i": 39, "p": { "1": 1.5 } },
        { "i": 18, "p": { "0": [6, 6, 6], "2": "Sphere", "ab": "User" } }
      ]
    }
  ]
}
```

- `v` — format version (`0`)
- `p` — skill / root properties
- `b[]` — branches
  - `p` — branch properties
  - `c[]` — branch conditions
  - `b[]` — timeline nodes
- node `i` — opcode
- node `p` — properties for that opcode
- node `t[]` — tags (optional)
- node `p.ab` — Targeting (optional)

Default values are stripped on export. If a key is missing, the UI is using its default.

## Targeting

Every node that has a Targeting dropdown stores it as **`p.ab`**.

| JSON | UI |
|---|---|
| *(omitted)* | Default |
| `"User"` | User |
| `"Target"` | Target |
| `"Projectile"` | Projectile |
| `"Last Hit"` | Last Hit |
| `"User Mouse"` | User Mouse |
| `"Nearest"` | Nearest |

Mapped name: `target`.

`ab` is not Attach To. Attach To is a different key (Hitbox `p.k`, Mesh FX `p.e`, Special FX `p.e`, …).

## Opcodes

| i | Name |
|---|---|
| 0 | AddAwakening |
| 1 | CameraShake |
| 2 | AddCooldown |
| 3 | AddHealth |
| 4 | Animation |
| 5 | Branch |
| 6 | CancelTypes |
| 7 | CancelTags |
| 8 | ChangeDisplay |
| 9 | Counter |
| 10 | Crater |
| 11 | CustomAnim |
| 12 | Dialogue |
| 13 | DomainEffect |
| 14 | Effect |
| 15 | Grab |
| 16 | HitCancel |
| 17 | HitDetect |
| 18 | Hitbox |
| 19 | LightningFX |
| 20 | Look |
| 21 | Loop |
| 22 | MeshFX |
| 23 | Modifier |
| 25 | Particle |
| 26 | Points |
| 27 | Projectile |
| 28 | Pushback |
| 29 | Skill |
| 30 | Sound |
| 31 | SpecialFX |
| 33 | StateV1 |
| 34 | State |
| 36 | Variant |
| 38 | Velocity |
| 39 | Wait |
| 40 | Move |

## Root / branch / condition

Root `p`:

| key | name |
|---|---|
| 1 | name |
| 2 | displayName |
| 3 | toolTip |
| 4 | cooldown |
| 5 | speedMultiplier |
| 6 | damageMultiplier |
| 7 | ragdollMultiplier |
| 8 | knockbackMultiplier |
| 9 | walkspeedDivider |
| a | extraA |
| e | allowedItems |
| f | flag |

Branch `p`:

| key | name |
|---|---|
| 1 | name |
| 2 | autoBranch |
| 3 | displayName |
| 4 | toolTip |
| 5 | flashColor |
| e | kind |
| f | priority |

Condition `p`:

| key | name |
|---|---|
| 1 | isAirborne |
| 6 | isSpaceDown |
| 7 | isHoldingKey |
| 8 | isAwakened |
| 9 | hasAwakening |
| a | healthInRange |
| b | holdingItems |
| c | pointsQuery |
| d | attackState |

## Hitbox (`i = 18`)

Confirmed against SB ^_^:

| key | name | notes |
|---|---|---|
| 0 | size | `[X,Y,Z]`. Box uses this ^_^. Sphere still writes leftover size. |
| 1 | radius | only when Shape is Sphere |
| 2 | shape | `"Box"` (default, omitted) or `"Sphere"` |
| 3 | position | |
| 4 | orientation | |
| 5 | destroyPower | default `0` |
| 6 | havocScale | default `0` |
| 7 | bypassEverything | |
| 8 | maxHits | default `1` |
| 9 | duration | default `0` |
| b | blockable | |
| c | dealer | |
| d | healthInRange | `{g, v:[min,max]}` |
| e | branchHit | |
| f | branchBlocked | |
| h | hitRagdoll | |
| k | attachTo | `"Character"` default, also `"Root Part"` |
| q | pointsQuery | |
| t | counterTier | default `0` |
| x | blockAngle | default `90` |
| y | hitUser | |
| ab | target | Targeting |
| ht | havocType | `"Erase"` / `"Debris"` / `"Inner Erase"` |
| fb | branch | |
| hp | hitPriority | |
| hs | hitStyle | |
| m | mode | |

## Special FX (`i = 31`)

One shared property area lol. It **relabels the same keys** depending on `p.1` (the effect type). Switching effects does **not** clear unused keys, so leftovers from the previous type stay in the JSON (w optimization).

`p.1` omitted = **Highlight** (the default).

| key | mapped | notes |
|---|---|---|
| 1 | effect | `"Outline"`, `"FieldOfView"`, `"Mesh"`, … |
| 2 | amount | also Mesh Id / Day Offset, stored as a string |
| 3 | speed | also Texture Id, stored as a string |
| 4 | color | color sequence `[[t,R,G,B,A], …]` |
| 5 | transparency | number sequence. UI also uses this as Strength / Brightness |
| 6 | scale | number sequence |
| 7 | time | `[min, max]`. UI often shows it as `min.max` |
| a | saturation | number sequence. Color Correction uses this |
| b | flag | reused bool: Always On Top / Is Neon / Allow Camera Shake |
| e | attachTo | `"Character"` default (omitted), also `"Head"`, `"Root Part"` |
| s | smoothing | `"Linear"` default (omitted), `"Bezier"`, `"Constant"` |
| r | range | |
| ox / oy / oz | positionX/Y/Z | number sequences |
| sx | size | 3-axis size/scale curves |
| rx / ry / rz | orientationX/Y/Z | number sequences |
| ab | target | Targeting |
| av | visibleTo | `"Non-User"`, `"Non-Target"` |
| ad | adornee | `"User"` (Blur). omitted = default |

Effect types:

`Highlight`, `Outline`, `FieldOfView`, `ScreenGlass`, `Distortion`, `Reverberate`, `Vignette`, `ColorCorrection`, `Mesh`, `Light`, `Daytime`, `Afterimage`, `Blood`, `Camera`, `Blur`, `Visibility`, `Infinity`

Mapped in-game Names to Keys:

| Effect | Fields |
|---|---|
| Highlight | Position (ox/oy/oz), Size (sx), Smoothing (s), Visible To (av), Attach To (e), Targeting (ab), Color (4) |
| Outline | Always On Top (b), Color (4), Transparency (5), Time (7), Visible To, Attach To, Targeting |
| FieldOfView | Range (r), Smoothing (s), Visible To, Attach To, Targeting |
| ScreenGlass | Amount (2), Scale (6), Time (7), Visible To, Attach To, Targeting |
| Distortion | Amount (2), Scale (6), Time (7), Visible To, Attach To, Targeting |
| Reverberate | Color (4), Strength (5), Time (7), Position (ox/oy/oz) |
| Vignette | Color (4), Transparency (5), Scale (6), Time (7) |
| ColorCorrection | Color (4), Brightness (5), Saturation (a), Time (7), Range (r) |
| Mesh | Mesh Id (2), Texture Id (3), Is Neon (b), Color (4), Transparency (5), Scale (6), Time (7) |
| Light | Is Neon (b), Color (4), Transparency (5), Time (7), Range (r), Position |
| Daytime | Day Offset (2), Duration (7), Range (r) |
| Afterimage | Duration (7), Range (r) |
| Blood | Is Neon (b), Color (4), Transparency (5), Time (7), Range (r), Position, Orientation, Smoothing, Visible To, Attach To, Targeting |
| Camera | Amount (2), Speed (3), Color (4), Transparency (5), Time (7), Visible To, Attach To, Targeting |
| Blur | Allow Camera Shake (b), Time (7), Range (r), Position, Orientation, Smoothing (s), Adornee (ad), Visible To, Attach To |
| Visibility | Scale (6), Time (7), Range (r), Visible To, Attach To, Targeting |
| Infinity | Color (4), Transparency (5), Scale (6), Time (7), Visible To, Attach To, Targeting |

Full per-effect field list also lives under `nodes.SpecialFX.variants` in `mapping.json`.

## Value wrappers

Most optional / toggleable fields look like:

```json
{ "g": 0, "v": <value> }
```

- `g = 0` — field on, value is `v`
- `g = 1` — field off / unused / defaulted (UI greyed)

Bare numbers, bools, and arrays are constants.

Curves:

- number sequence: `[[t, value, envelope], ...]`
- color sequence: `[[t, R, G, B, A], ...]`

Unknown keys should be left as-is so new fields are not dropped.

## Notes/Repo Usage

• I am not responsible for however people use this repo, this is for Educational Purposes only <3.

• Not EVERYTHING may be mapped yet, but most.

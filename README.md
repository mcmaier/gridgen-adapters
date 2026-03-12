# MacGizmo GridGen — Community Adapter Repository

> Adapter library for the [MacGizmo GridGen](https://macgizmo.com/gridgen) Parametric Prototype PCB Generator.

This repository contains SMD adapter definitions in JSON format that can be loaded in the GridGen editor. Each adapter describes the THT through-hole pins, SMD pads, traces, solder mask openings, and silkscreen features of a footprint.

Use the [adapter-editor](https://macgizmo.com/gridgen/adapter-editor.html) to edit adapters or to easily import [KiCAD](https://www.kicad.org)-Footprints and subcircuit PCBs.

---

## Directory Structure

```
gridgen-adapters/
├── adapters/
│   ├── soic/
│   │   ├── soic8.json
│   │   └── soic16.json
│   ├── msop/
│   │   └── msop12.json
│   ├── tssop/
│   │   └── tssop16.json
│   ├── qfn/
│   │   └── qfn32.json
│   └── misc/
│       └── example.json
├── schema/
│   └── adapter.schema.json    ← JSON Schema for validation
├── CONTRIBUTING.md
└── README.md
```

Category folders correspond to the `category` field in the JSON (lowercase).

---

## Adapter Format

Each file contains a **JSON array** with one or more adapter objects.  
Multiple objects in one file make sense when variants of a footprint belong together (e.g. MSOP-12 for 2.54 mm and 2.0 mm pitch).

### Required Fields

| Field | Type | Description |
|---|---|---|
| `id` | `string` | Unique ID, kebab-case, e.g. `"msop12"` |
| `name` | `string` | Display name in the editor |
| `category` | `string` | Category, matches subfolder name, e.g. `"MSOP"` |
| `pitch` | `number` | THT grid pitch in mm, typically `2.54` or `2.0` |
| `color` | `string` | Display color in the editor (hex), e.g. `"#60c0a0"` |
| `throughPins` | `array` | THT pins in the grid — see below |
| `features` | `object` | Mandatory features (always rendered) — see below |
| `outline` | `object` | Adapter outer dimensions `{ width, height }` in mm |
| `outlineOffset` | `object` | Outline offset `{ x, y }` in mm |
| `widthPins` | `number` | Number of reserved grid columns |
| `heightPins` | `number` | Number of reserved grid rows |
| `silkLabel` | `object` | Label `{ text, x, y, rotation, height }` |
| `overlay` | `boolean` | Show PNG overlay (Needs separate png file) |

### Optional Fields

| Field | Type | Description |
|---|---|---|
| `optionalFeatures` | `object` | Optional features (e.g. thermal pad), user-toggleable |

---

## Coordinate System

- Origin `(0, 0)` corresponds to the grid position `(col 0, row 0)` of the adapter
- col and row (for THT-pins) are in steps of main pitch
- X increases to the **right**, Y increases **upward** (Gerber convention)
- Same for COL and ROW: Internally counting from bottom left to top right 
- All dimensions in **millimeters**

### `throughPins` — THT Pins

Array of grid positions that are placed as THT pads on the perfboard.
1st pin (label = 1) has a square pad, others are round.

```json
"throughPins": [
  { "col": 0, "row": 0, "label": "1" },
  { "col": 1, "row": 0, "label": "2" }
]
```

| Field | Type | Description |
|---|---|---|
| `col` | `number` | Grid column (0-based) |
| `row` | `number` | Grid row (0-based) |
| `label` | `string` | Pin designator printed on silkscreen |

---

### `features` and `optionalFeatures`

Both objects share the same structure:

```json
{
  "copper":    [ ...Feature... ],
  "mask":      [ ...Feature... ],
  "silk":      [ ...Feature... ],
  "silkText":  [ ...Feature... ],
  "copperBack":[ ...Feature... ],
  "drills":    [ ...Drill... ]
}
```

#### Feature Types

**`pad`** — rectangular SMD pad (center-origin):
```json
{ "type": "pad", "x": 2.93, "y": 5.435, "w": 1.45, "h": 0.4 }
{ "type": "pad", "x": 2.93, "y": 5.435, "w": 1.45, "h": 0.4, "rotation": 45 }
```

**`trace`** — copper trace:
```json
{ "type": "trace", "x1": 2.92, "y1": 5.46, "x2": 2.41, "y2": 5.46, "w": 0.25 }
```

**`circle`** — circle (e.g. pin 1 marker on silk):
```json
{ "type": "circle", "x": 2.5, "y": 6.2, "d": 0.5 }
```

**`poly`** — polyline (e.g. silk outline):
```json
{ "type": "poly", "points": [{ "x": 0, "y": 0 }, { "x": 5, "y": 0 }, { "x": 5, "y": 3 }] }
```

**`text`** — text (use in `silkText` only):
```json
{ "type": "text", "text": "GND", "x": 1.2, "y": 0.5, "height": 0.8, "rotation": 0 }
```

#### Drills

```json
{ "x": 4.58, "y": 4.81, "drill": 0.3, "size": 0.6 }
```

| Field | Description |
|---|---|
| `drill` | Drill diameter in mm |
| `size` | Pad outer diameter in mm |

---

## Loading Adapters in the GridGen Editor

1. Open the editor: [macgizmo.com/gridgen/adapter-editor](https://macgizmo.com/gridgen/adapter-editor)
2. Click **"Load JSON"** → select a local `.json` file
  **OR** click "Open file", select KiCAD footprint or PCB and click on the corresponding button to import it.
3. Edit the adapter features
4. Click **"Save JSON"** → export the finished file

---

## Contributing (Pull Request)

1. Fork this repository
2. Create a new JSON file in the appropriate category folder  
   Naming convention: `{package}{pincount}.json`, e.g. `soic16.json`
3. Validate the JSON against the schema (see `schema/adapter.schema.json`)
4. Open a pull request

### PR Requirements

- `id` is unique and not already present in the repo
- Coordinates are in mm, origin at grid position (col 0, row 0)
- Adapter has been visually verified in the GridGen editor
- File is placed in the correct category folder

---

## License

[MIT](LICENSE) — free to use, including in commercial projects.  
Contributions are published under the same license.
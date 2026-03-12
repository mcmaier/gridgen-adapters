# MacGizmo GridGen — Community Adapter Repository

> Adapter-Bibliothek für den [MacGizmo GridGen](https://macgizmo.com/gridgen) Parametric Prototype PCB Generator.

Dieses Repository enthält SMD-Adapter-Definitionen im JSON-Format, die im GridGen-Editor geladen werden können. Jeder Adapter beschreibt die THT-Durchkontaktierungen, SMD-Pads, Leiterbahnen, Lötstopplack und Seidendruckfeatures eines Footprints.

---

## Ordnerstruktur

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
│   └── adapter.schema.json    ← JSON Schema zur Validierung
├── CONTRIBUTING.md
└── README.md
```

Kategorie-Ordner entsprechen dem `category`-Feld im JSON (Kleinbuchstaben).

---

## Adapter-Format

Jede Datei enthält ein **JSON-Array** mit einem oder mehreren Adapter-Objekten.  
Mehrere Objekte in einer Datei sind sinnvoll, wenn Varianten eines Footprints zusammengehören (z.B. MSOP-12 für 2,54 mm und 2,0 mm Pitch).

### Pflichtfelder

| Feld | Typ | Beschreibung |
|---|---|---|
| `id` | `string` | Eindeutige ID, kebab-case, z.B. `"msop12"` |
| `name` | `string` | Anzeigename im Editor |
| `category` | `string` | Kategorie, entspricht Unterordner, z.B. `"MSOP"` |
| `pitch` | `number` | THT-Raster in mm, typisch `2.54` oder `2.0` |
| `color` | `string` | Darstellungsfarbe im Editor (Hex), z.B. `"#60c0a0"` |
| `throughPins` | `array` | THT-Pins im Grid — siehe unten |
| `features` | `object` | Pflicht-Features (immer gerendert) — siehe unten |
| `outline` | `object` | Außenmaß des Adapters `{ width, height }` in mm |
| `outlineOffset` | `object` | Versatz des Outlines `{ x, y }` in mm |
| `widthPins` | `number` | Anzahl belegter Grid-Spalten |
| `heightPins` | `number` | Anzahl belegter Grid-Reihen |
| `silkLabel` | `object` | Beschriftung `{ text, x, y, rotation, height }` |
| `overlay` | `boolean` | PNG-Overlay anzeigen |

### Optionale Felder

| Feld | Typ | Beschreibung |
|---|---|---|
| `optionalFeatures` | `object` | Optionale Features (z.B. Thermal-Pad), vom User zuschaltbar |

---

### `throughPins` — THT-Pins

Array von Grid-Positionen, die als THT-Pads auf dem Perfboard belegt werden.

```json
"throughPins": [
  { "col": 0, "row": 0, "label": "1" },
  { "col": 1, "row": 0, "label": "2" }
]
```

| Feld | Typ | Beschreibung |
|---|---|---|
| `col` | `number` | Spalte im Grid (0-basiert) |
| `row` | `number` | Reihe im Grid (0-basiert) |
| `label` | `string` | Pin-Bezeichnung auf dem Silk |

---

### `features` und `optionalFeatures`

Beide Objekte haben dieselbe Struktur:

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

#### Feature-Typen

**`pad`** — rechteckiges SMD-Pad (center-origin):
```json
{ "type": "pad", "x": 2.93, "y": 5.435, "w": 1.45, "h": 0.4 }
{ "type": "pad", "x": 2.93, "y": 5.435, "w": 1.45, "h": 0.4, "rotation": 45 }
```

**`trace`** — Leiterbahn:
```json
{ "type": "trace", "x1": 2.92, "y1": 5.46, "x2": 2.41, "y2": 5.46, "w": 0.25 }
```

**`circle`** — Kreis (z.B. Pin-1-Markierung auf Silk):
```json
{ "type": "circle", "x": 2.5, "y": 6.2, "d": 0.5 }
```

**`poly`** — Polyline (z.B. Silk-Outline):
```json
{ "type": "poly", "points": [{ "x": 0, "y": 0 }, { "x": 5, "y": 0 }, { "x": 5, "y": 3 }] }
```

**`text`** — Text (nur in `silkText`):
```json
{ "type": "text", "text": "GND", "x": 1.2, "y": 0.5, "height": 0.8, "rotation": 0 }
```

#### Drills (nur in `optionalFeatures` oder `features`)

```json
{ "x": 4.58, "y": 4.81, "drill": 0.3, "size": 0.6 }
```

| Feld | Beschreibung |
|---|---|
| `drill` | Bohrerdurchmesser in mm |
| `size` | Pad-Außendurchmesser in mm |

---

### Koordinatensystem

- Ursprung `(0, 0)` entspricht der Grid-Position `(col 0, row 0)` des Adapters
- X wächst nach **rechts**, Y wächst nach **oben** (Gerber-Konvention)
- Alle Maße in **Millimeter**

---

## Adapter im GridGen Editor laden

1. Editor öffnen: [macgizmo.com/gridgen/adapter-editor](https://macgizmo.com/gridgen/adapter-editor)
2. **"JSON laden"** → lokale `.json`-Datei auswählen
3. Adapter bearbeiten und testen
4. **"JSON speichern"** → fertige Datei exportieren

---

## Beitrag leisten (Pull Request)

1. Fork dieses Repositories
2. Neue JSON-Datei im passenden Kategorieordner anlegen  
   Namensschema: `{package}{pincount}.json`, z.B. `soic16.json`
3. JSON gegen das Schema validieren (siehe `schema/adapter.schema.json`)
4. Pull Request erstellen

### Anforderungen an einen PR

- `id` ist eindeutig und noch nicht im Repo vorhanden
- Koordinaten sind in mm, Ursprung oben links
- Adapter wurde im GridGen Editor visuell geprüft
- Datei liegt im richtigen Kategorieordner

---

## Lizenz

MIT — freie Verwendung, auch in kommerziellen Projekten.  
Beiträge werden unter derselben Lizenz veröffentlicht.

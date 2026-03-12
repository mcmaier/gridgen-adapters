# Contributing to gridgen-adapters

Danke für deinen Beitrag zur GridGen Community-Bibliothek!

## Schnellstart

1. **Fork** → lokaler Clone → neue JSON-Datei anlegen
2. Im [GridGen Adapter Editor](https://macgizmo.com/gridgen/adapter-editor) testen
3. Pull Request gegen `main`

## Namenskonventionen

| Was | Schema | Beispiel |
|---|---|---|
| Dateiname | `{package}{pins}.json` | `soic16.json` |
| `id` | kebab-case | `"soic-16"` |
| Kategorieordner | Großbuchstaben | `SOIC/` |

## Checkliste vor dem PR

- [ ] `id` ist eindeutig (nicht bereits im Repo)
- [ ] Adapter visuell im Editor geprüft
- [ ] Koordinatenursprung oben links, Einheit mm
- [ ] `throughPins` stimmen mit Grid-Pitch überein
- [ ] Datei im richtigen Kategorieordner
- [ ] JSON ist valide (z.B. via `jsonschema` oder VSCode)

## Fragen?

Issue öffnen oder direkt auf [macgizmo.com](https://macgizmo.com) melden.

# VLA JSON ↔ CSV converter

A single-page browser tool that converts a Label Studio **VLA video export** to a
`master` / `unique` spreadsheet and back.

- **JSON → spreadsheet** — upload a VLA `.json`, get either
  - an `.xlsx` with a **master** tab (one row per T3 child: `t3 start/end`, parent
    `t2 start/end`, `t2 id`, `t2`, `t3 id`, `t3`) and a **unique** tab (distinct T2 /
    T3 descriptions with occurrence counts). In the `.xlsx`, the master `t2` / `t3`
    cells are `VLOOKUP` formulas keyed by id — edit a description on the **unique**
    tab and every matching master row updates.
  - or two plain `.csv` files (no linking — CSV has no tabs or formulas).
- **spreadsheet → JSON** — upload the `.xlsx` (or a single `master` `.csv`) and get a
  partial JSON in the same template. The **unique** tab is treated as the source of
  truth for description text. Every T2 frame range is preserved exactly; the only
  loss is the per-T3 body-part label (rebuilt as `"segment"`).

Everything runs in the browser. No file is uploaded anywhere.

## Files

| file | purpose |
|---|---|
| `index.html` | the whole tool |
| `xlsx.full.min.js` | vendored [SheetJS](https://sheetjs.com) 0.18.5 (xlsx read/write) |

## Local use

Open `index.html` in any browser (double-click), or serve the folder:

```
python3 -m http.server 8000
# then open http://localhost:8000
```

## Deploy (GitHub Pages)

This folder is a static site — push it to a repo and enable Pages (Settings →
Pages → Deploy from branch → `main` / root). See the deploy steps in the project
notes.

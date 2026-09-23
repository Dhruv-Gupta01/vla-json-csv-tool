# VLA / Unified JSON ↔ CSV converter

A single-page browser tool that converts either source format to a `master` /
`unique` spreadsheet and back. The source format is **auto-detected**:

- **unified** — `{tier1:{text}, tier2:[{id,start_frame,end_frame,description}],
  tier3:[{id,parent_t2,start_frame,end_frame,body_part,description,object_refs}], ...}`.
  The T3→T2 parent link is the explicit `parent_t2` id. `body_part` is a real field,
  so it round-trips exactly and appears as its own `body part` column.
- **legacy** — a Label Studio VLA export (`annotations[0].result`, `"action"` vs
  body-part `timelinelabels`). The T3→T2 link is inferred by frame-range containment
  (no explicit parent id in this format). The per-T3 body-part label isn't carried in
  `master`, so on rebuild it comes back as a placeholder (`"segment"`).

- **JSON → spreadsheet** — upload one `.json`, get either
  - an `.xlsx` with a **master** tab (one row per T3 child: `t3 start/end`, parent
    `t2 start/end`, `t2 id`, `t2`, `t3 id`, `t3`, plus `body part` for the unified
    schema) and a **unique** tab (distinct T2 / T3 descriptions with occurrence
    counts, two independent lists side by side). In the `.xlsx`, master's `t2` / `t3`
    cells are `VLOOKUP` formulas keyed by id — edit a description on the **unique**
    tab and every matching master row updates.
  - or two plain `.csv` files (no linking — CSV has no tabs or formulas).
- **spreadsheet → JSON** — upload the `.xlsx` (or a single `master` `.csv`) and get a
  partial JSON in the same template as the source. Output schema is auto-detected
  from the sheet: a `body part` column present → rebuilds **unified**; absent →
  rebuilds **legacy** VLA. Editing rule: a master `t2`/`t3` cell you typed over
  directly always wins; an untouched cell (still holding the formula) keeps
  following the `unique` tab. Every T2 frame range is preserved exactly. Fields not
  carried by the sheet (objects, relations, keypoints, QC, tier1 caption) are
  emitted empty.

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

Push to `main`; Pages redeploys automatically (~1 min):

```
git commit -am "…"
git push
```

Live: https://dhruv-gupta01.github.io/vla-json-csv-tool/

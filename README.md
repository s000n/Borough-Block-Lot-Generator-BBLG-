
# BBL Generator

A browser-based tool for appending NYC Borough-Block-Lot (BBL) identifiers to survey spreadsheets. Built during the 2026 SYEP internship at the NYC Landmarks Preservation Commission, developed collaboratively with Claude (Anthropic) as an AI coding assistant. Problem framing, requirements, testing, and iteration were conducted against real LPC survey data.

## What it does

Takes an Excel file containing Borough, Block, and Lot columns and appends a computed 10-digit BBL identifier as a new column. All other data is preserved unchanged.

**BBL format:** `B OOOOO LLLL` — always 10 digits, zero-padded.

| Component | Width | Example |
|-----------|-------|---------|
| Borough code | 1 digit | 3 (Brooklyn) |
| Block number | 5 digits | 01795 |
| Lot number | 4 digits | 0006 |

Borough codes: 1 = Manhattan, 2 = Bronx, 3 = Brooklyn, 4 = Queens, 5 = Staten Island.

Example: Brooklyn, Block 1795, Lot 6 → `3017950006`

## Usage

Open `bbl_tool.html` in any modern browser. No installation, no server required. All processing is local — no data is uploaded anywhere.

1. Drop an Excel file onto the upload zone or click to browse
2. Check the processing log — verify detected columns and borough source
3. Use the Borough override dropdown if auto-detection is incorrect
4. For multi-sheet files, use the Sheet selector to switch between tabs
5. Review the preview table and diff check output
6. Click **Download Excel** to save the output file

## Column detection

Borough, Block, and Lot columns are matched by header name with fuzzy matching for whitespace variants (e.g. "Bloc k" matches "Block"). Borough is resolved in priority order: Borough column in data → filename keyword → manual override dropdown → Brooklyn (with warning).

## Multi-lot support

The Lot column may contain multiple lot values in several formats — all are handled:

| Input | Output |
|-------|--------|
| `21` | single BBL |
| `21, 42` | two BBLs, comma-separated |
| `18 and 19` | two BBLs, comma-separated |
| `5-12` | BBLs for lots 5 through 12, comma-separated |

Lot ranges larger than 10 are flagged as `RANGE_TOO_LARGE` rather than expanded — these are likely data entry errors and require manual review.

## Flagging

Rows that cannot produce a valid BBL are flagged in the output cell and logged with the row number:

| Flag | Cause |
|------|-------|
| `LOT_ERROR:value` | Lot value is an Excel serial date or otherwise implausible (e.g. "Aug-16" auto-converted by Excel) |
| `RANGE_TOO_LARGE:range` | Lot range exceeds 10 entries |

Flagged rows appear in the processing log and are counted in the **flagged** stats counter.

## Output panels

- **Processing log** — column detection, borough source, date columns found, per-row warnings
- **Stats bar** — total rows / BBL filled / blank / flagged / errors
- **Preview** — first 20 rows showing key columns and the BBL column
- **Diff check** — compares output against input on all non-BBL, non-date columns and flags any unexpected changes

## Known limitations

- Cell formatting (colors, fonts, column widths) is not preserved — data values and structure are preserved
- Date serials below 30000 (before ~October 1982) are not converted, to avoid misinterpreting plain year values (e.g. "1967") as dates
- Lot values that are Excel-auto-converted dates (e.g. "Aug-16" → 45885) are flagged but require manual correction in the source file
- Requires both Block and Lot columns; files missing either cannot produce BBL output
- Processes one sheet at a time; multi-sheet files require switching sheets and downloading separately
- Output is always `.xlsx` regardless of input format

## Files

```
bbl_tool.html   — self-contained single-file application
README.md       — this file
LICENSE         — MIT license
```

## Dependencies

Loaded from CDN at runtime:
- [SheetJS](https://sheetjs.com/) (xlsx) v0.18.5 — Excel read/write
- IBM Plex Mono + Inter via Google Fonts

## Background

LPC survey spreadsheets contain building records with separate Borough, Block, and Lot fields but no pre-computed BBL. The BBL is required for spatial joins against NYC property databases (MapPLUTO, the Buildings dataset) to attach BIN numbers and other property identifiers. This tool automates BBL construction across heterogeneous survey files that vary in column naming, structure, and data entry conventions.

## License

MIT — see `LICENSE`.

# BBL Generator

A browser-based tool for appending NYC Borough-Block-Lot (BBL) identifiers to survey spreadsheets. Built during the 2026 SYEP internship at the NYC Landmarks Preservation Commission.

## What it does

Takes an Excel file containing separate Borough, Block, and Lot columns and appends a computed 10-digit BBL identifier as a new column. The output file is otherwise identical to the input.

**BBL format:** `B OOOOO LLLL` — always 10 digits, zero-padded.

| Component | Width | Example |
|-----------|-------|---------|
| Borough code | 1 digit | 3 (Brooklyn) |
| Block number | 5 digits | 01795 |
| Lot number | 4 digits | 0006 |

Borough codes: 1 = Manhattan, 2 = Bronx, 3 = Brooklyn, 4 = Queens, 5 = Staten Island.

Example: Brooklyn, Block 1795, Lot 6 → `3017950006`

## Usage

Open `bblg_tool.html` in any modern browser. No installation, no server, no internet connection required after the page loads. All processing happens locally — no data is uploaded anywhere.

1. Drop an Excel file onto the upload zone, or click to browse
2. Verify the detected columns in the processing log
3. Use the Borough override dropdown if auto-detection is incorrect
4. For multi-sheet files, use the Sheet selector to switch between tabs
5. Review the preview table and diff check output
6. Click **Download Excel** to save the output file

## Column detection

The tool detects Borough, Block, and Lot columns by header name, with fuzzy matching for whitespace variants (e.g. "Bloc k" matches "Block"). If no Borough column is found, it attempts to infer the borough from the filename using known LPC survey area names, then falls back to Brooklyn with a warning.

## Features

- Fuzzy column header matching
- Borough detection from column data, filename keywords, or manual override
- Multi-sheet support via sheet selector
- Date column preservation — Excel serial dates in columns named "date" are converted to MM/DD/YYYY strings
- Explicit cell typing on output to prevent Excel from coercing hyphenated addresses or BBL numbers
- Diff check panel flags any unexpected changes between input and output

## Known limitations

- Date serials below 30000 (before October 1982) are not converted, to avoid misinterpreting plain year values (e.g. "1967") as dates
- Requires Block and Lot columns to construct a BBL; files without both will not produce output
- Processes one sheet at a time; multi-sheet files require switching sheets manually and downloading separately

## Files

```
bblg_tool.html   — self-contained single-file application
README.md       — this file
LICENSE         — MIT license
```

## Dependencies

Loaded from CDN at runtime:
- [SheetJS](https://sheetjs.com/) (xlsx) v0.18.5 — Excel read/write
- IBM Plex Mono + Inter via Google Fonts

## Background

LPC survey spreadsheets contain building records with separate Borough, Block, and Lot fields but no pre-computed BBL. The BBL is required for spatial joins against NYC property databases (e.g. MapPLUTO, the Buildings dataset) to attach BIN numbers and other property identifiers. This tool automates the BBL construction step across heterogeneous survey files that may differ in column naming and structure.

## License

MIT — see `LICENSE`.

Developed collaboratively with Claude (Anthropic) as an AI coding assistant. Problem framing, requirements, testing, and iteration were conducted against real LPC survey data.

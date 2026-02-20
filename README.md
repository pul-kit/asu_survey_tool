# Survey CSV Processor & Visualizer

`survey_tool.html` is a single-file, in-browser tool for processing BCBA survey-export CSVs and generating:
- Student-level metrics tables and charts (filtered by full name)
- Class-level metrics tables and charts (across all uploaded files)
- Processed CSV downloads (single file or ZIP)

## Current UI sections

- **Student Metrics**
  - Enter a full name and generate student-focused table/graph output from matching files only.
- **Class-level Metrics**
  - Generate class table/graph output from all uploaded files.
- **Processed Downloads**
  - Download one processed CSV or all processed CSVs as `processed_csvs.zip`.

## Quick start

1. Open `survey_tool.html` in a browser.
2. Upload one or more CSVs.
3. For student output, enter **Full Name** exactly (trimmed, case-insensitive) and click:
   - `Create Table` or
   - `Create Graph`
4. For class output, click:
   - `Create Class Table` or
   - `Create Class Graph`
5. Use **Processed Downloads** to export processed data.

## Filename handling and ordering

The tool expects filenames like:

- `YYYY_Semester_SPEXXX.csv`
- `YYYY_Semester_SPEXXX_AdditionalIfNeeded.csv`

File order in outputs is deterministic:
1. Year ascending
2. Semester order: `SpringA`, `SpringB`, `SpringC`, `SummerA`, `SummerB`, `SummerC`, `FallA`, `FallB`, `FallC`
3. Filename tie-breaker

## Processing behavior

For each uploaded CSV, the tool:

1. Detects the header row as the row with the highest non-empty cell count.
2. Canonicalizes known headers (assessment headers and `All-*` summary headers).
3. Keeps only `Response Status = Completed` rows if that column exists.
4. Builds `Full Name` from `First Name` and `Last Name` (stored lowercase) when available.
5. Keeps only:
   - `Full Name` (if available)
   - numbered assessment headers (`A-1` through `I-*` as present)
   - computed section stats (`A-Mean`, `A-Median`, `A-Min`, `A-Max`, `A-StdDev`, etc.)
   - overall stats (`All-Mean`, `All-Median`, `All-Min`, `All-Max`, `All-StdDev`)
6. Appends generated class-metric rows for class-level output.

## Student outputs

Student table/graph uses only files where the entered full name exists.

- **Student table**
  - Rows: auto-selected assessment/stat fields
  - Columns: matched files
  - Cell value: unique non-empty values joined by ` | `
- **Student graphs**
  - One chart per selected field
  - One bar per matched file
  - If all values are numeric after filtering: bar = average
  - Otherwise: bar = non-empty count

## Class outputs

Class outputs always use all uploaded files.

- **Class table**
  - Rows: generated class metric row keys (for example `A-1-Mean`)
  - Columns: all files
- **Class graphs**
  - One chart per class metric row key
  - One bar per file

## Course-aware green highlighting

Green highlighting applies to:
- Student table cells
- Student graph bars
- Class table cells
- Class graph bars

Highlight mapping by course code in filename:

1. `SPE525` -> `C`, `D`
2. `SPE526` -> `B`
3. `SPE528` -> `F`, `G`, `H`
4. `SPE529` -> `F`, `G`, `H`
5. `SPE530` -> `I`
6. `SPE563` -> `A`
7. `SPE567` -> `E`

For mapped letters, highlighting includes:
- numbered fields (for example `C-1`, `D-2`)
- section stats (`-Mean`, `-Median`, `-Min`, `-Max`, `-StdDev`)
- class metric variants derived from those sources (for example `C-1-Mean`, `C-Mean-StdDev`)

Unmapped course codes (for example `SPE527`) receive no highlighting.
`All-*` fields are not highlighted.

## Exports and limitation

- Table exports are CSV text only.
- Processed downloads are CSV text only.
- CSV cannot store visual styling (cell colors), so green shading appears in the UI only.

## Dependencies

Loaded from CDN in `survey_tool.html`:
- PapaParse
- Chart.js
- JSZip

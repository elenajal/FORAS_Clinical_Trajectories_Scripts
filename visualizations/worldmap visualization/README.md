# Worldmap Visualisation — Clinical Studies

`worldmap_visualisation.ipynb` is a Python notebook that generates a choropleth world map showing the geographic distribution of included studies/samples, based on each study's reported location.

## What this notebook does

1. **Loads the extraction spreadsheet** (`Clinical_extraction_final.xlsx`) and reads its `Location` column.
2. **Splits multi-country entries**: some rows list more than one country (e.g. `"USA, Canada"`, `"UK and Netherlands"`). These are split on commas, semicolons, `+`, `/`, or the word "and", then exploded into one row per country so each contributing country is counted separately.
3. **Normalizes country names to ISO3 codes** using the `country_converter` package, with a manual override dictionary for names that aren't automatically recognized (e.g. `"UK"` → `GBR`, `"South Korea"` → `KOR`, `"Taiwan"` → `TWN`, `"Palestine"` → `PSE`). Any location string that still can't be mapped is printed as a warning for manual inspection.
4. **Counts studies per country** (ISO3 code) and builds a choropleth map with `plotly.express`, using a yellow-to-red color scale (`YlOrRd`) and a natural earth projection, styled with Times New Roman fonts.
5. **Exports three output files** to a `worldmap_outputs/` folder:
   - `world_meta_analysis.html` — interactive Plotly map
   - `world_meta_analysis.png` — static image of the map (rendered from the HTML via `html2image`, avoiding the need for `kaleido`)
   - `country_counts.csv` — per-country study counts, sorted descending

## Requirements

```bash
pip install pandas plotly country_converter html2image openpyxl
```
- `country_converter` — maps country name variants to standardized ISO3 codes
- `plotly` — builds the interactive choropleth
- `html2image` — renders a static PNG from the saved HTML (this avoids needing `kaleido`, which `plotly`'s built-in static image export normally requires)
- `openpyxl` — needed by `pandas.read_excel` for `.xlsx` files

`html2image` uses a headless Chrome/Chromium browser under the hood; make sure a compatible browser is installed and discoverable on the system running the notebook, or the PNG export step will fail.

## Data requirements

The notebook expects an Excel file at:
```
../data.xlsx
```
relative to the notebook's location 

Required column:
| Column | Purpose |
|---|---|
| `Location` | Free-text country/location name(s) per study; may contain multiple countries separated by `,`, `;`, `+`, `/`, or "and" |

## How to run

Open and run the notebook top to bottom in Jupyter:
```bash
jupyter notebook worldmap_visualisation.ipynb
```
The notebook will:
1. Load and clean the location data
2. Print a warning listing any location strings it couldn't map to an ISO3 code (extend the `overrides` dictionary in cell 2 if this happens)
3. Display the interactive map inline
4. Write the HTML, PNG, and CSV outputs to `worldmap_outputs/`

## Output

- **`world_meta_analysis.html`** — interactive choropleth (hover to see country and study count)
- **`world_meta_analysis.png`** — static 1200×800 snapshot of the map, suitable for a manuscript figure
- **`country_counts.csv`** — a simple table of ISO3 code and study count, sorted from most to fewest studies

## Notes / caveats

- If a location string cannot be resolved to ISO3 either automatically or via the `overrides` dictionary, that country is silently excluded from the map and counts (after the warning is printed) — check the warning output and extend `overrides` as needed for your dataset's location naming conventions.
- Studies reporting multiple countries contribute to the count for **each** listed country, so the sum of `study_count` across countries can exceed the number of studies/rows in the original dataset.
- This notebook only produces the geographic visualization; it does not perform any statistical modelling (see the companion prevalence and moderator-analysis `.Rmd` files, and the timeline-visualisation notebook, for those parts of the project).

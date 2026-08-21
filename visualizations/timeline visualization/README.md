# Timeline Visualisation — Clinical Studies

`Timeline_visualisation_clinical.ipynb` is a Python notebook that generates a publication-style timeline figure showing, for each included study, when PTSD symptom/outcome assessments were taken relative to trauma or treatment onset, alongside the treatment period and treatment type.

## What this notebook does

1. **Loads the extraction spreadsheet** (`Clinical_extraction_final.xlsx`) into a `pandas` DataFrame.
2. **Defines a `Timeline` class** that:
   - Buckets each study into a treatment-type group (`CBT`, `exposure`, `mix`, or `other`) based on the `Treatment_type` column, each with a fixed color from a colorblind-friendly palette.
   - Parses each study's `Time_points_plot` column — a comma-separated string of tokens, where each token is either a single time point (e.g. `"3"`) or a range (e.g. `"3:6"`) — into plottable point/range values.
   - Sorts studies by their last (latest) assessment time point, then alphabetically by study name, so the plot reads from longest to shortest follow-up.
   - Converts `Treatment_length` from weeks to months (÷ 4.33) to draw a shaded treatment-period rectangle behind each study's timeline.
3. **Plots each study as a horizontal timeline row**:
   - A thin connecting line spans from the earliest to latest assessment
   - Single time points are drawn as scatter markers; time ranges are drawn as thicker line segments
   - A shaded green rectangle marks the treatment period (from time 0 to treatment length in months)
   - Line/marker color encodes treatment type
   - A vertical black reference line marks time = 0
4. **Supports two rendering modes**:
   - `show()` — a single tall single-panel plot with all studies
   - `show_split(...)` — splits studies across multiple panels/columns (e.g., for fitting on an A4 landscape page), with shared x-axis, configurable panel layout, spacing, and a combined legend
5. **Saves the figure** to disk (e.g. `timeline_panels.png`) at a specified DPI.

## Requirements

```bash
pip install pandas numpy matplotlib openpyxl
```
(`openpyxl` is required by `pandas.read_excel` for `.xlsx` files; the notebook's first cell also runs `!pip install pandas numpy matplotlib`.)

The notebook uses "Times New Roman" as the plotting font (`mpl.rcParams['font.family']`) — this must be available on the system running the notebook, or matplotlib will silently fall back to a default font.

## Data requirements

The notebook expects an Excel file at:
```
../data.xlsx
```
relative to the notebook's location.

Required/expected columns:
| Column | Purpose |
|---|---|
| `Study` | Study label, used for y-axis ticks |
| `Time_points_plot` | Comma-separated list of time points and/or ranges (e.g. `"0, 3, 6:12"`) defining assessment timing |
| `Treatment_type` | Used to bucket/color studies as CBT, exposure, mix, or other |
| `Treatment_length` | Treatment duration in **weeks**; converted to months for plotting |

Studies missing `Time_points_plot` values are treated as having no plottable timepoints; missing `Treatment_type` values fall into the "other" (gray) group; missing `Treatment_length` values are treated as 0 (no treatment-period rectangle drawn).

## How to run

Open and run the notebook top to bottom in Jupyter:
```bash
jupyter notebook Timeline_visualisation_clinical.ipynb
```
The last cell instantiates the class and generates the figure:
```python
tl = Timeline(data_for_timeline)
tl.show_split(num_panels=1, ncols=2, reverse_panels=True,
              savepath="timeline_panels.png", panel_wspace=0.5,
              row_step=10, x_pad=0.05)
```
Adjust `num_panels`, `ncols`, `max_per_panel`, `row_step`, and figure size arguments to fit the number of studies in your dataset.

## Output

A timeline figure (displayed inline and saved to `timeline_panels.png` in the working directory) with:
- One row per study, showing its assessment time points/ranges relative to time 0
- A shaded treatment-period band per study (where treatment length is available)
- Color-coded lines/markers by treatment type (CBT, exposure, mix, other)
- A shared legend for treatment type and the treatment-period shading

## Notes / caveats

- Token parsing (`_parse`) expects each time-point token to be either a bare number or a colon-separated range (`"a:b"`); malformed tokens are silently dropped.
- `show_split()`'s panel splitting can be controlled either by `num_panels` (fixed number of panels, studies split evenly) or by `max_per_panel` (fixed panel size, number of panels inferred) — only one of these logics is used depending on whether `num_panels` is passed.
- This notebook only produces the visualization; it does not perform any statistical modelling (see the companion prevalence and moderator-analysis `.Rmd` files for that).

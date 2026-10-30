# seroGlance

A quick look at multiplex serology data processed with [SerolyzeR](https://cran.r-project.org/package=SerolyzeR): density plots, antigen correlations, a sample × antigen heatmap, and boxplots by metadata variables such as age, sex, site or collection date.

**Open the tool:** https://leacavalli.github.io/SeroGlance/

## Background

seroGlance was originally developed in the context of the [xSTAR project](https://wellcome.org/research-funding/funding-portfolio/funded-grants/reconstructing-transmission-intensity-arboviruses), which uses a specific multiplex bead assay described in [A Multiplex Serological Assay for the Detection of Antibody Responses to Arboviruses](10.3791/69259). It works with SerolyzeR output from any multiplex assay. Antigens are grouped into families by the text before the first `_` in their names (e.g. `MAL_`, `ARB_`).

## Data quality: filter upstream

**seroGlance does not filter your data.** Apart from leaving out standards, blanks, controls and empty wells flagged in the SerolyzeR output by default, it plots every value in the files you load.

Quality filtering is meant to happen upstream: remove low-quality test samples (for example wells with low bead counts or failed runs) and unreliable antigens from the SerolyzeR outputs before loading them. SerolyzeR itself does not remove low-count wells.

If you load raw SerolyzeR outputs instead, be aware that every plot, correlation and group comparison reflects the quality of that data, including any problem wells, plates or antigens.

## Your data stays on your computer

Everything runs in your web browser. Files you load are read locally and are never uploaded anywhere. The only thing the page downloads is its fonts, from Google Fonts. You can also download `index.html` and open it from your own computer; it then works offline, using your system fonts.

## Input files

Drop one or more CSV files written by SerolyzeR onto the page, or use **Choose files**:

| File | Written by |
|---|---|
| `<plate>_MFI.csv`, `<plate>_RAU.csv`, `<plate>_nMFI.csv` | `process_plate()`: one file per plate |
| `merged_<type>…csv` | `merge_plate_outputs()`: all plates in one table |
| A long table with an `antigen_label` column and `MFI_Median`, `RAU` and/or `nMFI` columns | e.g. a long-format export with one row per sample × antigen |

Whether a file holds MFI, RAU or nMFI is read from its file name. Antigen names have to be written the same way in every file to be combined.

**Which wells are plotted:** only test samples, by default. Wells named like `BLANK`, `1/400`, `S_1/400` or `EMPTY_H9`, and positive or negative control names, are treated as blanks, standards, empty wells or controls, following SerolyzeR's naming rules. If a file has a `sample_type` column, that column is used instead. Tick *Include standards, blanks, controls and empty wells* to plot every well.

## Metadata (optional)

To compare groups, add a CSV with one row per sample:

- **One column of sample IDs** that match the sample names in the SerolyzeR files. The column can have any name; seroGlance finds the one whose values match. Matching ignores upper/lower case and SerolyzeR's `.1` suffix on repeated names. You can pick a different column from the *Sample ID column* menu.
- **Any variables of interest**, for example:
  - text columns such as sex, site or region, used as groups as they are;
  - number columns such as age, cut into groups at cut-offs you can edit (age defaults to <5, 5–15, 15–25, 25–45 and ≥45);
  - date columns such as collection date, grouped by month or by year. Ambiguous dates like 03/04/2025 are read as day/month/year.

The file is recognised as metadata if its name contains "meta" or if it is mostly text columns. You can also load it with **Add metadata**.

## Views

- **Density**: the distribution of each antigen, for all samples pooled or for each plate. Hover a panel to read values; click it for a larger view and a summary table.
- **Correlations**: a matrix for up to 12 antigens of your choice, with Spearman or Pearson correlations, density plots on the diagonal and scatterplots below it.
- **Heatmap**: every sample × antigen in the order of the loaded files. Values are rescaled within each antigen, between its 1st and 99th percentiles.
- **By group**: boxplots of each selected antigen by one metadata variable, optionally split by a second one shown in colour. Click a panel for a larger view and a table of medians and quartiles you can copy.

All views can show MFI, RAU or nMFI, on a log or linear scale.

## Updating the site

seroGlance is a single file, `index.html`. To update the site, replace that file in this repository; GitHub Pages republishes it within a few minutes.

## Contact

Questions or problems: please open an issue in this repository.

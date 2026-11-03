<p align="center"><img src="seroglance-logo.svg" alt="seroGlance logo: a magnifying glass whose lens is an antigen-coated bead, with a bound antibody and a labelled detection antibody, over a density curve" width="160"></p>

# seroGlance

A quick visual look at multiplex serology results, antigen by antigen, straight from [SerolyzeR](https://cran.r-project.org/package=SerolyzeR) output: density plots, antigen correlations, a sample × antigen heatmap, and boxplots by metadata variables such as age, sex, site or collection date.

**Open the tool:** https://leacavalli.github.io/SeroGlance/

No installation and no account are needed. The page opens with simulated example data until you load your own files. The **About** button on the page summarises everything below.

## Background

seroGlance was originally developed in the context of the [xSTAR project](https://wellcome.org/research-funding/funding-portfolio/funded-grants/reconstructing-transmission-intensity-arboviruses), which uses the multiplex bead assay described in [A Multiplex Serological Assay for the Detection of Antibody Responses to Arboviruses](https://pubmed.ncbi.nlm.nih.gov/41284716/) (*JoVE*). It works with SerolyzeR output from any multiplex assay. Antigens are grouped into families by the text before the first `_` in their names (e.g. `MAL_`, `ARB_`).

## Data quality: filter upstream

**seroGlance does not filter your data.** Apart from leaving out standards, blanks, controls and empty wells by default, it plots every value in the files you load.

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

## MFI, RAU and nMFI

- **MFI (median fluorescence intensity):** the raw signal of each antigen's beads in a well, as read by the instrument. It is not corrected for differences between plates. SerolyzeR's merged tables are blank-adjusted: values below the plate's blank well are raised to the blank value.
- **RAU (relative antibody units):** each sample's MFI read off its plate's standard curve, a 5-parameter logistic fit to the standard dilution series, where the standard at dilution 1/x is worth 1,000,000/x RAU (1/50 = 20,000 RAU). RAU are comparable across plates run with the same standard; by default, SerolyzeR caps values above the highest standard at its RAU.
- **nMFI (normalised MFI):** each sample's MFI divided by the MFI of one standard dilution on the same plate (1/400 by default in SerolyzeR), so 1 means the same signal as that standard.

On the page, the selected value type is defined under the *Value* buttons, and hovering a button shows its definition.

## Metadata (optional)

To compare groups, add a CSV with one row per sample:

- **One column of sample IDs** that match the sample names in the SerolyzeR files. The column can have any name; seroGlance finds the one whose values match. Matching ignores upper/lower case and SerolyzeR's `.1` suffix on repeated names. You can pick a different column from the *Sample ID column* menu.
- **Any variables of interest**, for example:
  - text columns such as sex, site or region, used as groups as they are;
  - number columns such as age, cut into groups at cut-offs you can edit (age defaults to <5, 5–15, 15–25, 25–45 and ≥45);
  - date columns such as collection date, grouped by month or by year. Ambiguous dates like 03/04/2025 are read as day/month/year.

The file is recognised as metadata if its name contains "meta" or if it is mostly text columns. You can also load it with **Add metadata**.

## Example files

The [`examples`](examples) folder holds the simulated data the page shows when it opens (7 plates, 12 antigens), written in SerolyzeR's format:

- [`seroglance_example_merged_MFI.csv`](examples/seroglance_example_merged_MFI.csv): a merged MFI table, as written by `merge_plate_outputs()`, including blanks and standards;
- [`seroglance_example_metadata.csv`](examples/seroglance_example_metadata.csv): matching metadata (sex, age, site, region, collection date).

Drop both onto the page to try every view. The same files can be downloaded from the page itself (About → Example files, or the links in the example-data banner).

## Views

- **Density**: the distribution of each antigen, for all samples pooled or for each plate. Hover a panel to read values; click it for a larger view and a summary table.
- **Correlations**: a matrix for up to 12 antigens of your choice, with Spearman or Pearson correlations, density plots on the diagonal and scatterplots below it. Tick *Colour scatterplot points by plate* to give each plate its own colour; click a plate in the list under the matrix to highlight its points, and hover a scatterplot to see the plate and sample of the nearest point.
- **Heatmap**: every sample × antigen in the order of the loaded files. *Don't normalize* (the default) puts all antigens on one colour scale, so colours compare antigens with each other as well as samples; *Normalize by antigen* rescales each antigen between its own 1st and 99th percentiles, so colours compare samples within an antigen.
- **By group**: each selected antigen by one metadata variable, optionally split by a second one shown in colour, plotted as boxplots, beeswarms (one dot per sample) or density ridges (one density curve per group). Click a panel for a larger view and a table of medians and quartiles you can copy.

All views can show MFI, RAU or nMFI, on a log or linear scale.

**Exporting figures:** each view has an *Export figure* button (PNG or SVG) that saves what is on screen, and the larger view of a single antigen has *Download PNG* / *Download SVG*. Figures are always exported on a white background, even when the page is in dark mode. PNGs are saved at twice the screen resolution, for slides and documents; SVGs can be edited in Illustrator, Inkscape or PowerPoint. In the SVG of the correlation matrix and the heatmap, the plot itself is an embedded image, while titles and labels stay editable text.

## Updating the site

seroGlance is a single file, `index.html`; the logo, browser-tab icon and example-file downloads are built into it. To update the site, replace that file in this repository; GitHub Pages republishes it within a few minutes.

## Logo

The logo shows a quick look through a magnifying glass whose rim is an assay bead. The bead is coated with copies of one antigen; a serum IgG is bound to one copy and is in turn bound by a phycoerythrin-labelled anti-IgG, whose fluorescence is the MFI read by the instrument. A density curve sits inside the lens. Files: `seroglance-logo.svg` and `seroglance-logo.png`.

## Contact

Questions or problems: please open an issue in this repository.

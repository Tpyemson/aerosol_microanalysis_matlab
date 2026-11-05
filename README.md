# Aerosol Microanalysis MATLAB Tools

MATLAB scripts and GUIs for single-particle analysis of atmospheric aerosols, developed during PhD research in atmospheric aerosol chemistry at Purdue University (Laskin group). The tools cover three common steps in a microspectroscopy workflow:

- **STXM/NEXAFS**: measuring particle sizes on STXM image stacks and fitting carbon K-edge NEXAFS spectra
- **CCSEM/EDX**: cleaning elemental data and building particle size distributions (PSDs) by composition
- **Clustering**: grouping spectra with k-means and silhouette analysis

Most scripts use file and sheet pickers, so there is nothing to hard-code: run the script and choose your data when prompted.

## Repository contents

```
stxm/
  stxm_particle_diameter_gui.m   Interactive particle sizing on STXM stacks
  NEXAFS_FIT_GUI_v2.m            Peak deconvolution GUI for C K-edge NEXAFS
ccsem/
  CCSEM_element_threshold.m      Zero out sub-threshold EDX element values
  PSD_AutoPlot.m                 Per-sample PSDs by OVF bin and particle class
  PSD_AutoPlot_Groups.m          Same, plus pooled 2-group comparison mode
clustering/
  spectra_kmeans_clustering.m    K-means clustering of spectra with silhouettes
```

## Script overview

### stxm/stxm_particle_diameter_gui.m
Loads a STXM stack (`.hdr` plus `.xim` or `.stxm` images), lets you scroll through energy slices, draw a freehand outline around a particle and records its area-equivalent diameter, `D = sqrt(4A/pi)`, in nm using the pixel size from the header. Measurements from several samples accumulate and export to a CSV with continuous particle IDs (appending to an existing CSV is supported).

### stxm/NEXAFS_FIT_GUI_v2.m
A modern `uifigure` front end for NEXAFS peak fitting: load a spectrum, optionally smooth it (Savitzky-Golay), define Gaussian, asymmetric Gaussian, Lorentzian and step-function peaks in an editable table, fit, and export every plotted curve to Excel. Fit sessions and peak parameter sets can be saved and reloaded.

> **Dependency note:** this file replaces only the GUI layer of the original `NEXAFS_FIT_GUI`. The fitting engine and helpers (`Nexafs_Decon`, `loadFit_functions`, `C_K_Edge`, `S_golay_filt`, `nexafs_table_legend`, `math_models_nexafs`, `spec_matfile_struct`) are not included here and must be on the MATLAB path.

### ccsem/CCSEM_element_threshold.m
Sets EDX element values below a detection threshold (default 0.5 atomic %) to zero across a chosen column range, so noise-level signals are not counted as detected elements. Writes a new `_modified` file and leaves the original unchanged.

### ccsem/PSD_AutoPlot.m and PSD_AutoPlot_Groups.m
Read per-particle diameters from CCSEM Excel workbooks (one sheet per sample) and bin them into log-spaced `dN/dlogDp` distributions, two ways:

- by **organic volume fraction (OVF)** bin, and
- by **particle class** (OC, OCIn, In, OCEC, OCInEC).

Each panel is a stacked-bar PSD with a 100%-composition inset. Results are exported to Excel and saved as `.fig` and 300 dpi `.png`.

`PSD_AutoPlot_Groups.m` adds a **group comparison mode**: pool samples into two named groups (for example, dense vs. dilute plumes), normalize each to peak = 1, and plot a 2x2 comparison with particle counts and median diameters. It can also produce the per-sample output, so it covers everything `PSD_AutoPlot.m` does.

See the header comment in each PSD script for the exact expected sheet layout.

### clustering/spectra_kmeans_clustering.m
Clusters spectra stored in Excel (energy in column 1, one sample per column). Runs k-means for K = 2 to 5, shows silhouette plots and PCA maps for each K, then lets you choose K. Writes `Clustered_Output` (spectra sorted by cluster) and `Cluster_Averages` (mean spectrum per cluster) back into the workbook.

> **Note:** this script writes new sheets into the input workbook. Work on a copy if you need the original untouched.

## Requirements

| Script | MATLAB | Toolboxes |
|---|---|---|
| stxm_particle_diameter_gui | R2020a+ | Image Processing |
| NEXAFS_FIT_GUI_v2 | R2020a+ | Signal Processing (plus whatever the fitting engine uses) |
| CCSEM_element_threshold | R2019a+ | none |
| PSD_AutoPlot, PSD_AutoPlot_Groups | R2020a+ | none |
| spectra_kmeans_clustering | R2020a+ | Statistics and Machine Learning |

## Getting started

1. Download or clone the repository:
   ```
   git clone https://github.com/<tpyemson>/aerosol-microanalysis-matlab.git
   ```
2. In MATLAB, add the folders to your path:
   ```matlab
   addpath(genpath('path/to/aerosol-microanalysis-matlab'))
   ```
3. Run a tool by name, for example:
   ```matlab
   stxm_particle_diameter_gui
   PSD_AutoPlot_Groups
   ```
   Follow the file and sheet dialogs.

Output files from the PSD scripts are written to MATLAB's current folder.

## Citation

If these tools are useful in your work, please cite this repository:

> Olayemi, T. E. (2026). *Aerosol Microanalysis MATLAB Tools*. GitHub. https://github.com/<tpyemson>/aerosol-microanalysis-matlab

## License

Released under the MIT License (see `LICENSE`). The external NEXAFS fitting routines listed above are not covered by this license and remain the work of their original authors.

## Contact

Temitope (Tope) Olayemi, Department of Chemistry, Purdue University

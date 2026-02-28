# Hyppy for QGIS — Complete Workflow Guide

**Hyperspectral Analysis Tools | Version 2.8 | February 2026**

---

## Table of Contents

1. [Overview](#1-overview)
2. [Installation](#2-installation)
3. [Step 2: Generate WoM Rasters](#3-step-2-generate-wom-rasters)
4. [Step 2b: Wavelength of Minimum (Custom Range)](#4-step-2b-wavelength-of-minimum-custom-range)
5. [Step 2c: Wavelength Mapping](#5-step-2c-wavelength-mapping)
6. [6 - Feature Extraction](#6-feature-extraction-group-6)
7. [3 - Decision Tree Classification](#7-decision-tree-classification-group-3)
8. [5 - Spectral Processing Tools](#8-spectral-processing-tools-group-5)
9. [4 - SAM Classification](#9-sam-classification-group-4)
10. [Common Wavelength Ranges](#10-common-wavelength-ranges)
11. [Version History](#11-version-history)
12. [References](#12-references)

---

## 1. Overview

The Hyppy for QGIS plugin provides hyperspectral analysis tools accessed via the QGIS Processing Toolbox. The toolbox is organised as follows:

- **1 - Workflow Guide** — open this guide in your browser
- **2 - Generate WoM Rasters** — generate WoM rasters and wavelength maps
- **3 - Decision Tree Classification** — classify minerals and physical properties
- **4 - SAM Classification** — Spectral Angle Mapper classification
- **5 - Spectral Processing Tools** — band ratios, depths, convex hull removal, band math
- **6 - Feature Extraction** — one-click WoM + wavelength map + legend for standard absorption features

### Plugin Structure

| Algorithm | Toolbox Group | Purpose |
|-----------|--------------|---------| 
| **Step 1 - Open Workflow Guide** | 1 - Workflow Guide | Open this guide in your browser |
| **Step 2a - Generate All Three WoM Rasters** | 2 - Generate WoM Rasters | Generate WoM-A, WoM-B and WoM-C in one step |
| **Step 2a - Generate WoM-A (750-1300 nm)** | 2 - Generate WoM Rasters | Generate WoM-A individually |
| **Step 2a - Generate WoM-B (1850-2100 nm)** | 2 - Generate WoM Rasters | Generate WoM-B individually |
| **Step 2a - Generate WoM-C (2100-2400 nm)** | 2 - Generate WoM Rasters | Generate WoM-C individually |
| **Step 2b - Wavelength of Minimum (custom range)** | 2 - Generate WoM Rasters | Extract absorption feature wavelengths and depths for any range |
| **Step 2c - Wavelength Mapping** | 2 - Generate WoM Rasters | Create RGB visualisation from absorption features |
| **FE1 - 1480W** | 6 - Feature Extraction | WoM 1440-1520 nm, map 1471-1491 nm |
| **FE2 - 1550W** | 6 - Feature Extraction | WoM 1510-1610 nm, map 1520-1563 nm |
| **FE3 - 1760W** | 6 - Feature Extraction | WoM 1730-1790 nm, map 1751-1764 nm |
| **FE4 - 2080D** | 6 - Feature Extraction | WoM 2060-2100 nm, map 2075-2085 nm |
| **FE5 - 2160D** | 6 - Feature Extraction | WoM 2138-2179 nm, map 2159-2166 nm |
| **FE6 - 2200W** | 6 - Feature Extraction | WoM 2120-2245 nm, map 2185-2215 nm |
| **FE7 - 2250W** | 6 - Feature Extraction | WoM 2230-2280 nm, map 2248-2268 nm |
| **FE8 - 2290W** | 6 - Feature Extraction | WoM 2270-2320 nm, map 2279-2338 nm |
| **FE9 - 2320W** | 6 - Feature Extraction | WoM 2295-2345 nm, map 2300-2340 nm |
| **FE10 - 2350W** | 6 - Feature Extraction | WoM 2310-2370 nm, map 2320-2366 nm |
| **FE11 - 2390W** | 6 - Feature Extraction | WoM 2375-2435 nm, map 2377-2406 nm |
| **DT Mineral 2100-2400** | 3 - Decision Tree Classification | Classify minerals in 2100–2400 nm range |
| **DT Albedo** | 3 - Decision Tree Classification | Classify brightness using per-pixel mean reflectance |
| **DT Fedrop** | 3 - Decision Tree Classification | Classify iron drop intensity from R(1600)/R(1310) ratio |
| **DT Illcryst** | 3 - Decision Tree Classification | Classify illite crystallinity and smectite minerals |
| **DT Ill-Kaol** | 3 - Decision Tree Classification | Classify illite vs kaolinite ratio |
| **DT Mineral Map** | 3 - Decision Tree Classification | Classify 17 mineral classes using WoM-B + WoM-C |
| **Spectral Angle Mapper (SAM)** | 4 - SAM Classification | Classify pixels against a spectral library |
| **Convex Hull Removal** | 5 - Spectral Processing Tools | Continuum removal using convex hull fitting |
| **Band Ratio** | 5 - Spectral Processing Tools | Single-band ratio (Band1/Band2) by wavelength |
| **Band Ratios (Sequential)** | 5 - Spectral Processing Tools | All-band sequential ratios with delta |
| **Band Depths** | 5 - Spectral Processing Tools | Per-band absorption feature depths |
| **Band Math** | 5 - Spectral Processing Tools | NumPy expression over image bands (i1, i2...) |
| **Spectra Math** | 5 - Spectral Processing Tools | Per-pixel spectral expression (S1, S2...) |

---

## 2. Installation

Copy the `Hyppy_for_QGIS` folder to your QGIS plugins directory:

- **Windows:** `%APPDATA%\QGIS\QGIS3\profiles\<profile>\python\plugins\`
- **Linux:** `~/.local/share/QGIS/QGIS3/profiles/<profile>/python/plugins/`
- **macOS:** `~/Library/Application Support/QGIS/QGIS3/profiles/<profile>/python/plugins/`

Restart QGIS, then enable the plugin via **Plugins > Manage and Install Plugins**. The Hyppy tools will appear in the Processing Toolbox under the Hyppy provider.

### Requirements

- QGIS 3.16 or later
- Python 3.x with NumPy and SciPy (included with QGIS)
- GDAL/OGR (included with QGIS)

---

## 3. Step 2: Generate WoM Rasters

This group generates the three Wavelength of Minimum (WoM) rasters required by the decision tree classifiers, and provides tools for custom wavelength range analysis and wavelength mapping. Run the WoM generation step first whenever processing a new hyperspectral dataset.

Use **Step 2a - Generate All Three WoM Rasters** to produce WoM-A, WoM-B, and WoM-C in a single operation, or run the individual Step 2a algorithms if only specific ranges need to be regenerated.

### 3.1 Required Input

The hyperspectral spectral cube — the original reflectance image with full spectral range and wavelength metadata (ENVI format or equivalent). All Step 2a algorithms accept the cube as their only input.

### 3.2 WoM Raster Specifications

| Name | Wavelength Range | Used by | Notes |
|------|-----------------|---------|-------|
| **WoM-A** | 750 – 1300 nm | Future classifiers | Iron oxide and hydroxyl features |
| **WoM-B** | 1850 – 2100 nm | dt_illcryst, dt_ill_kaol, dt_mineral_map | Water and carbonate shoulder; illite crystallinity denominator |
| **WoM-C** | 2100 – 2400 nm | dt_mineral_map, dt_2100_2400, dt_fedrop, dt_illcryst, dt_ill_kaol | Primary clay mineral absorption region |

### 3.3 Step 2a Algorithms

**Generate All Three WoM Rasters**
Runs the Wavelength of Minimum algorithm three times in sequence on a single hyperspectral cube, producing WoM-A, WoM-B, and WoM-C in one operation. This is the recommended starting point for any new dataset.

**Generate WoM-A / WoM-B / WoM-C (individual)**
Three individual algorithms for regenerating a single WoM raster without reprocessing the others. Each is pre-configured with the correct wavelength range; the only inputs required are the hyperspectral cube and an output path.

All Step 2a algorithms use Division continuum removal (recommended), extract 3 features per pixel by default (1–9 selectable), and honour the Bad Band List if present in the ENVI metadata. The output raster has 2 × N bands: wavelength and depth for each feature, sorted deepest first. Decision tree classifiers use Band 1 (primary feature) only.

---

## 4. Step 2b: Wavelength of Minimum (Custom Range)

Analyses each pixel's spectrum within a user-defined wavelength range. Applies convex hull continuum removal, locates local minima, and fits parabolas for sub-band-resolution wavelength and depth values. Use this when you need a WoM raster for a wavelength range other than the three presets in Step 2a.

**Parameters:**

| Parameter | Typical Value | Notes |
|-----------|--------------|-------|
| **Input Image** | Hyperspectral raster | Must have wavelength metadata |
| **Start Wavelength (nm)** | 2000 | Begin analysis window |
| **End Wavelength (nm)** | 2500 | End analysis window |
| **Continuum Removal** | Division | Division recommended for minerals |
| **Number of features (1–9)** | 1–3 | 1 for single mineral, 3+ for multi-mineral |
| **Broad feature fitting** | Off | On for broad absorption features |
| **Use Bad Band List** | On if available | Excludes noisy bands flagged in ENVI metadata |

**Output:** A multi-band GeoTIFF containing wavelength/depth pairs for each requested feature. Band 1 = interpolated min. wavelength, Band 2 = interpolated depth, and so on for additional features. Features are sorted by depth (deepest first).

---

## 5. Step 2c: Wavelength Mapping

Takes a Wavelength of Minimum output and creates an RGB image. Colour (hue) represents absorption wavelength position and brightness represents absorption depth.

**Parameters:**

| Parameter | Typical Value | Notes |
|-----------|--------------|-------|
| **Input (Step 2a or 2b output)** | WoM GeoTIFF | Uses Band 1 and Band 2 directly |
| **Min Wavelength for Display (nm)** | 2100 | Blue end of colour stretch |
| **Max Wavelength for Display (nm)** | 2350 | Red end of colour stretch |
| **Min Depth for Display** | 0.0 | Lower bound of brightness stretch |
| **Max Depth for Display** | 0.2 | Adjust for brightness (try 0.05–0.3) |

**Output:**

**Output Wavelength Map (RGB):** A 3-band RGB GeoTIFF. Hue encodes wavelength position (blue→green→yellow→red across the wavelength range) and brightness encodes absorption depth.

**Output Wavelength Map Legend (PNG):** A 2D colour legend image. The Y axis shows wavelength (nm) and the X axis shows absorption depth. The legend uses the same colour scheme as the wavelength map, allowing direct interpretation of map colours. After processing completes, the legend is automatically displayed in a floating dock window inside QGIS so that the map and legend can be viewed simultaneously. The dock window is resizable by dragging its edges.

---

## 6. Feature Extraction (Group 6)

The Feature Extraction tools are one-click pipelines that combine Wavelength of Minimum, Wavelength Mapping, and legend display into a single operation. Each tool targets a specific known absorption feature, with pre-configured wavelength ranges and map stretch values. Running a Feature Extraction tool produces three outputs: a WoM raster, an RGB wavelength map, and a colour legend PNG — and automatically displays the legend in a floating dock window.

Each tool is named after its characteristic absorption wavelength and feature type:
- **W** suffix — water or hydroxyl combination band
- **D** suffix — diagnostic mineral absorption feature

### 6.1 Feature Extraction Tools

| Tool | WoM Search Range | Map Colour Stretch | Characteristic Feature |
|------|-----------------|-------------------|----------------------|
| **FE1 - 1480W** | 1440 – 1520 nm | 1471 – 1491 nm | Water / hydroxyl combination band ~1480 nm |
| **FE2 - 1550W** | 1510 – 1610 nm | 1520 – 1563 nm | Hydroxyl overtone / organic ~1550 nm |
| **FE3 - 1760W** | 1730 – 1790 nm | 1751 – 1764 nm | Carbonate / organic combination ~1760 nm |
| **FE4 - 2080D** | 2060 – 2100 nm | 2075 – 2085 nm | Carbonate / AlOH diagnostic ~2080 nm |
| **FE5 - 2160D** | 2138 – 2179 nm | 2159 – 2166 nm | Kaolinite / dickite diagnostic ~2160 nm |
| **FE6 - 2200W** | 2120 – 2245 nm | 2185 – 2215 nm | AlOH absorption — illite / smectite / kaolinite region |
| **FE7 - 2250W** | 2230 – 2280 nm | 2248 – 2268 nm | AlOH / MgOH transition ~2250 nm |
| **FE8 - 2290W** | 2270 – 2320 nm | 2279 – 2338 nm | MgOH / carbonate ~2290 nm |
| **FE9 - 2320W** | 2295 – 2345 nm | 2300 – 2340 nm | Carbonate / dolomite ~2320 nm |
| **FE10 - 2350W** | 2310 – 2370 nm | 2320 – 2366 nm | Carbonate / chlorite ~2350 nm |
| **FE11 - 2390W** | 2375 – 2435 nm | 2377 – 2406 nm | MgOH / chlorite / serpentine ~2390 nm |

### 6.2 Inputs

| Parameter | Description |
|-----------|-------------|
| **Hyperspectral cube** | The original reflectance image with wavelength metadata |
| **Number of features (1–9)** | How many absorption minima to extract per pixel (default 1) |
| **Output WoM raster** | Path for the Wavelength of Minimum output |
| **Output Wavelength Map RGB** | Path for the colour-coded map |
| **Output Colour Legend (PNG)** | Optional — defaults to `<label>_legend.png` beside the WoM output |

### 6.3 Outputs

**WoM raster:** Multi-band GeoTIFF — Band 1 = wavelength of minimum (nm), Band 2 = feature depth. Additional band pairs follow for each requested feature, sorted deepest first.

**Wavelength Map RGB:** 3-band GeoTIFF where hue encodes wavelength position across the map stretch range and brightness encodes absorption depth. Ready for display in QGIS.

**Colour Legend PNG:** A 2D legend image with wavelength (nm) on the Y axis and depth on the X axis. The legend is saved as `<label>_legend.png` (e.g. `1480W_legend.png`) in the same folder as the WoM raster, unless an explicit path is specified. After processing the legend is automatically displayed in a floating dock widget within QGIS.

### 6.4 Workflow Tip

Feature Extraction tools are designed for rapid reconnaissance of a dataset. Run several tools across your scene to identify which absorption features are present and spatially coherent before committing to a full decision tree classification workflow.

---

## 7. Decision Tree Classification (Group 3)

The decision tree classifiers use pre-computed wavelength analysis outputs to assign mineral or physical property classes to each pixel. All classifiers require a minimum set of input data prepared in Step 2.

### 7.1 Required Input Data

**1. Hyperspectral spectral cube**
Required by dt_albedo and dt_fedrop to compute internal band ratios.

**2. WoM rasters from Step 2a:**

| WoM | Wavelength Range | Used by |
|-----|-----------------|---------| 
| **WoM-A** | 750 – 1300 nm | Future classifiers |
| **WoM-B** | 1850 – 2100 nm | dt_mineral_map, dt_illcryst, dt_ill_kaol (depth denominator) |
| **WoM-C** | 2100 – 2400 nm | dt_mineral_map, dt_2100_2400, dt_fedrop, dt_illcryst, dt_ill_kaol |

### 7.2 Decision Tree Algorithms

**DT Mineral 2100-2400 — Mineral Classification (2100–2400 nm)**
Classifies 16 mineral classes including kaolinite, muscovite, illite, pyrophyllite, alunite, and carbonate minerals. Input: WoM-C only. The algorithm uses the primary absorption wavelength (Band 1) and feature depth (Band 2) from the 2100–2400 nm wavelength map.
*Decision tree after van Ruitenbeek et al. (2025), Remote Sensing 17(15), 2555. https://doi.org/10.3390/rs17152555*

**DT Albedo — Brightness Classification**
Classifies pixels into four brightness classes (dark, med-dark, med-light, light) based on the mean reflectance across all bands. Thresholds are calculated dynamically using quartiles of the valid pixel distribution, so each image is classified relative to its own brightness range. Input: hyperspectral cube only.

**DT Fedrop — Iron Drop Classification**
Classifies iron drop intensity into eight classes (aspectral, no2100-2400, low through very-high). The fedrop value is computed internally as R(1600 nm) / R(1310 nm). Inputs: hyperspectral cube + WoM-C.

**DT Illcryst — Illite Crystallinity Classification**
Classifies illite crystallinity into eight classes ranging from smectite to muscovite (smect3, smect2, smect1, smect-ill, ill-smect, hx-ill, musc1, musc2). The illite crystallinity value (illx) is computed internally as depth(2100–2400 nm) / depth(1850–2100 nm). Inputs: WoM-B + WoM-C.

**DT Ill-Kaol — Illite / Kaolinite Classification**
Classifies the illite-to-kaolinite ratio into nine classes (aspectral, NO-ALOH, kaol4–kaol1, ill1–ill4). Thresholds based on analysis of reflectance spectra of illite and kaolinite mixtures. Inputs: WoM-B + WoM-C.

**DT Mineral Map — Mineral Map Classification**
Classifies pixels into 17 mineral classes covering illite/muscovite variants, kaolinite, phengite, Fe-chlorite, and chlorite. Branches on W1 (wavelength of deepest feature), W2 (wavelength of second deepest feature), and Ix (illite crystallinity). Inputs: WoM-B + WoM-C (WoM-C must have ≥2 features).

### 7.3 Outputs

Each classifier produces a palette-indexed integer class raster (GeoTIFF) as the primary output. Class names are embedded in the raster as a Raster Attribute Table (RAT), which QGIS reads to display class names in the layer legend. An optional RGB colour raster can also be requested for visual display.

---

## 8. Spectral Processing Tools (Group 5)

The six spectral processing tools can be applied independently to any hyperspectral raster. All tools read wavelength metadata from the ENVI domain first, with fallback to the default metadata domain and numeric band descriptions.

### 8.1 Convex Hull Removal (Continuum Removal)

Removes the spectral continuum from each pixel spectrum using a convex hull fit, isolating individual absorption features for analysis.

| Parameter | Description |
|-----------|-------------|
| **Input Image** | Hyperspectral raster with wavelength metadata |
| **Mode** | Divide: spectrum / hull (absorptions < 1.0) \| Subtract: 1 + (spectrum − hull) |
| **Cutoff Wavelength (nm)** | Restrict to bands below this wavelength. Set to 0 to use all bands. |
| **Use Bad Band List** | Exclude bands flagged in ENVI BBL metadata |

### 8.2 Band Ratio

Computes a single-band ratio image: Numerator band / Denominator band. Bands are specified by wavelength in nm; the nearest available band is selected automatically.

### 8.3 Band Ratios (Sequential)

Computes sequential band ratios across the full spectrum: `output[i] = input[i] / input[i + delta]`. Useful for highlighting spectral gradients and transitions.

### 8.4 Band Depths

Computes per-band absorption feature depths. For each centre band i: `top = (band[i-delta] + band[i+delta]) / 2`, `depth = (top - band[i]) / top`. Positive values indicate absorption.

### 8.5 Band Math

Evaluates an arbitrary Python/NumPy expression over one or more input rasters to produce a single-band output. Input images are referenced as `i1`, `i2`, ... (up to 20).

**Band access methods:**
- `i1.get_band(n)` — returns band n as a 2D array (0-based index)
- `i1.band(n)` — same but 1-based index
- `i1[n]` — same as get_band(n)
- `i1.bands` — number of bands
- `i1.wavelength` — wavelength array (or None if not available)

**Expression examples:**

| Expression | Description |
|------------|-------------|
| `i1.get_band(10) / i1.get_band(20)` | Band ratio (0-based, bands 11 and 21) |
| `(i1.get_band(30) - i1.get_band(20)) / (i1.get_band(30) + i1.get_band(20))` | Normalised difference (NDVI-style) |
| `i1.get_band(0) - i2.get_band(0)` | Band 1 difference between two images |
| `log(i1.get_band(4))` | Natural log of band 5 |

### 8.6 Spectra Math

Applies a per-pixel spectral expression using one or more input images. Each pixel spectrum is wrapped in a Spectrum object (S1, S2, ... up to 20).

**Spectrum object attributes:**
- `S1.spectrum` — 1D float64 array of band values for the current pixel
- `S1.wavelength` — 1D wavelength array
- `S1[n]` — value at band index n (0-based)
- `S1.wavelength2index(w)` — nearest band index for wavelength w nm

**Expression examples:**

| Expression | Description |
|------------|-------------|
| `S1 + S2` | Element-wise sum of two spectra |
| `Spectrum(S1.spectrum / S2.spectrum, S1.wavelength)` | Per-pixel spectral ratio |
| `Spectrum(np.log(S1.spectrum), S1.wavelength)` | Log of spectrum |

---

## 9. SAM Classification (Group 4)

The Spectral Angle Mapper classifies image pixels by measuring the spectral angle between each pixel and a set of reference spectra. SAM is insensitive to illumination variations, making it robust for mineral identification.

### 9.1 Prepare Input Data

- **Hyperspectral image** with wavelength metadata in ENVI header format.
- **Spectral library** in ENVI binary format (.sli + .hdr) or CSV text format (.csv, .txt).

### 9.2 Browse the Spectral Library (Optional)

1. Open SAM from Processing Toolbox > Hyppy > 4 - SAM Classification > Spectral Angle Mapper
2. Select your spectral library file
3. Check "List available spectra names (does not run SAM)"
4. Click Run — all spectra will be listed with index numbers in the log.

### 9.3 Configure the Spectra Filter

| Filter Pattern | Effect |
|---------------|--------|
| `Kaolinite*` | All spectra starting with 'Kaolinite' |
| `*alunite*` | Any spectrum containing 'alunite' (case-insensitive) |
| `Kaolinite*,Alunite*,Calcite*` | Multiple mineral groups |
| `1-10` | First 10 spectra by index (1-based) |
| `1,5,42` | Specific spectra by index |
| *(empty)* | Use all spectra in the library |

### 9.4 Select Similarity Measure

| Code | Measure | Notes |
|------|---------|-------|
| **SAM** | Spectral Angle Mapper | Recommended. Insensitive to illumination. |
| **BC** | Bray-Curtis Distance | Quantifies compositional dissimilarity. Range [0,1]. |
| **SID** | Spectral Information Divergence | Information-theoretic. Sensitive to spectral shape. |
| **ED** | Euclidean Distance | Sensitive to shape and magnitude. |
| **ID** | Intensity Difference | Sum of absolute differences. |

### 9.5 Outputs

- **Rule Image:** Multi-band raster with similarity values for each reference spectrum. Lower values indicate better matches.
- **Classification Image:** Single-band raster with class assignments (0 = unclassified, 1+ = class index).

### 9.6 Automatic Processing

The SAM algorithm automatically handles:
- **Wavelength resampling:** Reference spectra are interpolated to match image wavelengths when band counts differ.
- **Unit conversion:** Micrometres and nanometres are auto-detected and converted.
- **ENVI no-data handling:** Values of -1.23e34 are replaced with NaN and excluded from comparisons.

---

## 10. Common Wavelength Ranges

| Application | Start (nm) | End (nm) | Target Features |
|-------------|-----------|---------|----------------|
| **Clay minerals (AlOH)** | 2100 | 2250 | Kaolinite ~2165, Montmorillonite ~2210 |
| **Broad SWIR survey** | 2000 | 2500 | General mineral mapping |
| **Carbonate minerals** | 2300 | 2400 | Calcite ~2340, Dolomite ~2320 |
| **Iron oxides** | 850 | 1050 | Hematite ~860, Goethite ~920 |
| **Vegetation red edge** | 680 | 750 | Chlorophyll absorption |
| **Vegetation water** | 900 | 1100 | Leaf water content ~970 nm |

---

## 11. Version History

| Ver. | Title | Key Changes |
|------|-------|-------------|
| **2.8** | **Feature Extraction group** | Added 6 - Feature Extraction group with 11 one-click tools (FE1–FE11) covering key absorption features from 1440 to 2435 nm. Each tool runs WoM, generates a wavelength map with preset colour stretch, saves a named legend PNG (e.g. `1480W_legend.png`), and displays the legend in a floating dock. Annotated legend axes with bitmap font labels — no matplotlib dependency. Vectorised all colour mapping and legend rendering loops for speed. |
| **2.4** | **Toolbox restructure and bug fixes** | Toolbox reorganised: Step 1 (Workflow Guide), Step 2 (Generate WoM Rasters with Step 2a/2b/2c), 3 - Decision Tree Classification, 4 - SAM Classification, 5 - Spectral Processing Tools. Fixed other-9 mineral class unreachable in dt_2100_2400. Updated citation to van Ruitenbeek et al. (2025). Updated homepage URL to https://github.com/grantboxer/hyppy_plugin. Getting Started now opens USER_GUIDE.md in browser. |
| **2.3** | **Resizable legend window** | Legend dock window is now fully resizable by dragging its edges. Image scales to fill the dock. Minimum size 100×150 px. |
| **2.2** | **Fix legend not displaying** | Legend dock creation moved to postProcessAlgorithm(), fixing the legend not appearing after processing completed. |
| **2.0** | **Legend floating dock window** | Legend displayed automatically in a floating QDockWidget after processing. |
| **1.9** | **Legend user filename and display** | Legend output now has a dedicated user-supplied filename parameter. |
| **1.8** | **Toolbox group rename and plugin rename** | Plugin renamed to Hyppy_for_QGIS. Toolbox groups renamed to Step 1 / Step 2. |
| **1.5** | **Wavelength map legend** | Wavelength Mapping now generates a 2D colour legend PNG. |
| **1.4** | **Workflow A and plugin rename** | New Workflow A with four algorithms to generate WoM rasters directly from the cube. New dt_mineral_map classifier (17 mineral classes). |
| **1.3** | **Decision Tree Classifiers** | Restructured toolbox into five workflows A–E. Added dt_2100_2400, dt_albedo, dt_fedrop, dt_illcryst, dt_ill_kaol classifiers. |
| **1.2** | **Wavelength Bug Fix** | Fixed all six new tools not reading wavelengths from ENVI-format rasters. |
| **1.1** | **Six New 5 - Spectral Processing Tools** | Added Convex Hull Removal, Band Ratio, Band Ratios, Band Depths, Band Math, Spectra Math. |
| **0.9** | **Spectra Selection Filter** | Spectra filter with wildcard, index, and range support. Library browser to list available spectra. |
| **0.8** | **Robust ENVI Reader** | Direct .hdr parsing and binary reading. Tested against USGS Spectral Library V7 (402 spectra). |
| **0.5** | **Unit Auto-Detection** | Auto-converts µm ↔ nm wavelength units. |
| **0.4** | **Spectral Resampling** | Auto-resamples reference spectra to match image wavelengths. |
| **0.3** | **Wavelength Mapping Update** | Step 2 uses direct band indices from Step 1 output. |

---

## 12. References

Kruse, F.A. et al., 1993. The Spectral Image Processing System (SIPS) — Interactive Visualization and Analysis of Imaging Spectrometer Data. *Remote Sensing of Environment*, 44(2-3), pp.145–163.

Kokaly, R.F. et al., 2017. USGS Spectral Library Version 7. *U.S. Geological Survey Data Series* 1035, 61 p.

Bakker, W.H., 2010–2022. HyPy — Hyperspectral Python. University of Twente, Faculty ITC.

van Ruitenbeek, F.J.A. et al., 2025. A Knowledge-Based Strategy for Interpretation of SWIR Hyperspectral Images of Rocks. *Remote Sensing*, 17(15), 2555. https://doi.org/10.3390/rs17152555

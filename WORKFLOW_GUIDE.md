# Two-Step Wavelength Mapping Workflow

## Overview

The Hyperspectral Analysis Tools plugin provides a two-step workflow for mapping absorption features in hyperspectral imagery:

**Step 1: Wavelength of Minimum v2** — Extracts absorption feature positions and depths from the hyperspectral data.

**Step 2: Wavelength Mapping** — Creates a colour-coded RGB visualisation from the Step 1 output.

Both tools are found in the QGIS Processing Toolbox under **Hyperspectral Tools → Spectral Analysis**.

---

## Step 1: Wavelength of Minimum v2

### Purpose
Analyses each pixel's spectrum within a defined wavelength range to find absorption features. Applies convex hull continuum removal, locates local minima, and fits parabolas for sub-band-resolution wavelength and depth values.

### How to Run
1. Open **Processing → Toolbox**
2. Navigate to **Hyperspectral Tools → Spectral Analysis → Step 1: Wavelength of Minimum v2**
3. Set the parameters (see below)
4. Click **Run**

### Parameters

| Parameter | Description | Typical Value |
|-----------|-------------|---------------|
| **Input Hyperspectral Image** | Multi-band raster with wavelength metadata (ENVI header or band descriptions) | Your hyperspectral image |
| **Start Wavelength (nm)** | Beginning of the analysis window | 2000 (SWIR minerals) |
| **End Wavelength (nm)** | End of the analysis window | 2500 (SWIR minerals) |
| **Continuum Removal Mode** | Division (recommended), Subtraction, or None | Division |
| **Number of features (1–9)** | How many absorption features to detect per pixel | 1 for single feature, 3 for multi-mineral |
| **Broad feature fitting** | Uses a wider parabola fit window for broad features | Off for sharp features, On for broad |
| **Use Bad Band List (BBL)** | Exclude noisy bands flagged in ENVI metadata | On if BBL is available |
| **Output** | Path for the output GeoTIFF | e.g. `my_image_wav2.tif` |

### Output
The output contains **2 bands per feature**:

| Band | Name | Description |
|------|------|-------------|
| 1 | interpolated min. wav. | Parabola-fitted wavelength of deepest feature |
| 2 | interpolated depth | Depth (strength) of deepest feature |
| 3 | interpolated min. wav. 2 | Wavelength of 2nd deepest feature (if numfeatures ≥ 2) |
| 4 | interpolated depth 2 | Depth of 2nd deepest feature |
| ... | ... | Up to 9 feature pairs |

Features are sorted by depth (deepest first).

### Example Wavelength Ranges

| Application | Start (nm) | End (nm) | Notes |
|-------------|-----------|----------|-------|
| Clay minerals (AlOH) | 2100 | 2250 | Kaolinite ~2205, Montmorillonite ~2210 |
| Carbonate minerals | 2300 | 2400 | Calcite ~2340, Dolomite ~2320 |
| Iron oxides | 850 | 1050 | Hematite ~860, Goethite ~920 |
| Broad SWIR survey | 2000 | 2500 | General mineral mapping |
| Vegetation red edge | 680 | 750 | Chlorophyll absorption |

---

## Step 2: Wavelength Mapping

### Purpose
Takes the output from Step 1 and creates an RGB image where colour (hue) represents absorption wavelength position and brightness represents absorption depth. This provides an intuitive visual map of mineral or material distributions.

### How to Run
1. Open **Processing → Toolbox**
2. Navigate to **Hyperspectral Tools → Spectral Analysis → Step 2: Wavelength Mapping**
3. Set the parameters (see below)
4. Click **Run**

### Parameters

| Parameter | Description | Typical Value |
|-----------|-------------|---------------|
| **Input** | Output from Step 1 (must contain `interpolated min. wav.` and `interpolated depth` bands) | Step 1 output file |
| **Minimum Wavelength for Display (nm)** | Lower bound of the colour stretch | 2100 |
| **Maximum Wavelength for Display (nm)** | Upper bound of the colour stretch | 2350 |
| **Minimum Depth for Display** | Lower bound of the brightness stretch (0–1) | 0.0 |
| **Maximum Depth for Display** | Upper bound of the brightness stretch (0–1) | 0.2 |
| **Colour Scheme** | Rainbow (smooth blue→red) or Rainbow with Steps | Rainbow |
| **Output** | Path for the RGB GeoTIFF | e.g. `my_image_wavmap.tif` |

### Output
A 3-band RGB GeoTIFF image:
- **Colour (Hue)** = Wavelength position of the absorption feature
- **Brightness (Value)** = Depth/strength of the absorption feature
- **Black pixels** = No feature detected or zero depth

### Interpreting the Map
- **Same colour** = Same absorption wavelength = likely same mineral/material
- **Bright pixels** = Strong absorption features = higher concentration or clearer signal
- **Dark pixels** = Weak or absent absorption features
- **Colour gradients** = Shifts in absorption position = compositional variation

### Adjusting the Display
- **Narrow the wavelength range** to enhance subtle differences between similar minerals
- **Lower the depth max** to brighten the overall image
- **Raise the depth min** to suppress noise and weak features
- Use QGIS Layer Properties → Histogram on the Step 1 output bands to find good stretch values

---

## Complete Workflow Example

### Mineral Mapping in the 2.0–2.5 µm SWIR Range

1. **Load** your hyperspectral image into QGIS

2. **Run Step 1** with:
   - Start Wavelength: 2000
   - End Wavelength: 2500
   - Mode: Division
   - Number of features: 1
   - Broad feature fitting: Off
   - Output: `survey_wav2.tif`

3. **Inspect Step 1 output:**
   - Band 1 (wavelength): Check value range to set Step 2 colour stretch
   - Band 2 (depth): Check value range to set Step 2 brightness stretch

4. **Run Step 2** with:
   - Input: `survey_wav2.tif`
   - Wavelength range: 2150–2350 (or as determined from Step 1)
   - Depth range: 0.0–0.15 (adjust based on Step 1 histogram)
   - Colour scheme: Rainbow
   - Output: `survey_wavmap.tif`

5. **Interpret** the resulting colour map in QGIS

---

## Tips

- Always run Step 1 before Step 2 — Step 2 requires the specific band names produced by Step 1.
- For multi-feature analysis, Step 2 automatically uses the deepest feature (bands 1 and 2). To map the 2nd or 3rd feature, you would need to reorder the bands.
- The wavelength stretch range in Step 2 does not need to match the analysis range in Step 1. Use a narrower range in Step 2 to focus on specific minerals.
- If the map appears too dark, reduce the Maximum Depth value.
- If the map appears too bright or washed out, increase the Maximum Depth value.

---

*Plugin version 1.1.0 — Based on HyPy by Wim Bakker, University of Twente*

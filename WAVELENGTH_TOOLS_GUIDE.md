# Quick Start: Wavelength Analysis Tools

## Overview

The Wavelength of Minimum and Wavelength Mapping tools work together to identify and visualize absorption features in hyperspectral data. This is particularly useful for:

- **Mineral mapping** (clay minerals, carbonates, iron oxides)
- **Vegetation stress** detection
- **Material identification**
- **Quality assessment** of hyperspectral data

## Workflow

### Step 1: Wavelength of Minimum

This tool analyzes your hyperspectral image to find absorption features.

**What it does:**
- Applies convex hull removal to isolate absorption features
- Finds the wavelength position of minimum reflectance
- Calculates feature depth and shape characteristics
- Identifies multiple local minima

**How to use:**

1. Open Processing Toolbox
2. Navigate to: **Hyperspectral Tools → Spectral Analysis → Wavelength of Minimum**
3. Configure:
   - **Input Image:** Your hyperspectral image (must have wavelength metadata)
   - **Start Wavelength:** e.g., 2000 nm (start of analysis range)
   - **End Wavelength:** e.g., 2500 nm (end of analysis range)
   - **Mode:** Usually "Division" (recommended)
   - **Output:** Path for results (e.g., `wavelength_features.tif`)

4. Click **Run**

**Output:**
- 19-band image with feature characteristics
- Key bands:
  - Band 2: Interpolated minimum wavelength (most accurate)
  - Band 3: Interpolated depth
  - Band 4: Feature narrowness

**Example wavelength ranges:**
- **Clay minerals:** 2000-2500 nm
- **Carbonates:** 2200-2400 nm
- **Iron oxides:** 400-1000 nm
- **Vegetation (chlorophyll):** 650-750 nm
- **Vegetation (water):** 900-1000 nm or 1400-1500 nm

### Step 2: Wavelength Mapping

This tool creates a beautiful color visualization of the wavelength features.

**What it does:**
- Reads wavelength and depth from Step 1 output
- Maps wavelength to color (hue)
- Maps depth to brightness
- Creates RGB image for display

**How to use:**

1. Open Processing Toolbox
2. Navigate to: **Hyperspectral Tools → Spectral Analysis → Wavelength Mapping**
3. Configure:
   - **Input:** Output from Wavelength of Minimum (Step 1)
   - **Min Wavelength:** e.g., 2100 nm (for display)
   - **Max Wavelength:** e.g., 2350 nm (for display)
   - **Min Depth:** Usually 0.0
   - **Max Depth:** e.g., 0.2 (adjust to brighten image)
   - **Color Scheme:** Usually "Rainbow"
   - **Output:** Path for RGB map (e.g., `wavelength_map.tif`)

4. Click **Run**

**Output:**
- 3-band RGB image
- Colors represent different wavelengths
- Brightness represents feature strength

**Interpreting the map:**
- **Similar colors** = Similar absorption wavelengths = Likely same material
- **Bright pixels** = Strong absorption features
- **Dark pixels** = Weak or no features
- **Color patterns** = Different minerals or materials

## Complete Example: Mineral Mapping

Let's map clay minerals in a hyperspectral image:

### 1. Run Wavelength of Minimum
```
Input: my_hyperspectral_image.tif
Start Wavelength: 2000 nm
End Wavelength: 2500 nm
Mode: Division
Output: clay_features.tif
```

### 2. Examine the Results
Open `clay_features.tif` in QGIS:
- Look at Band 2 (interpolated wavelength) - shows where absorptions occur
- Look at Band 3 (depth) - shows how strong they are

### 3. Run Wavelength Mapping
```
Input: clay_features.tif
Min Wavelength: 2150 nm (kaolinite-smectite range)
Max Wavelength: 2250 nm
Min Depth: 0.0
Max Depth: 0.15 (adjust if too dark/bright)
Color Scheme: Rainbow
Output: clay_map_rgb.tif
```

### 4. Interpret the Map
Load `clay_map_rgb.tif`:
- **Blue-green colors (~2150-2170 nm):** Likely kaolinite
- **Yellow-orange colors (~2190-2210 nm):** Likely smectite/montmorillonite
- **Red colors (~2220-2240 nm):** Likely illite or muscovite
- **Bright areas:** Strong clay absorption
- **Dark areas:** Weak or no clay signature

## Tips and Tricks

### Optimizing Wavelength of Minimum

**Wavelength Range:**
- Narrow range (100-200 nm): Focus on specific features
- Wide range (500+ nm): General feature detection

**Continuum Removal Mode:**
- **Division (recommended):** Best for most cases, preserves feature shape
- **Subtraction:** Use for additive features
- **None:** Use original spectrum (no hull removal)

### Optimizing Wavelength Mapping

**Adjusting Wavelength Range:**
- Narrow range: Enhances subtle differences between similar materials
- Wide range: Shows broader material variations
- Match to your target materials

**Adjusting Depth Range:**
- **Too dark?** Decrease max depth (e.g., 0.2 → 0.1)
- **Too bright/washed out?** Increase max depth (e.g., 0.1 → 0.3)
- **Use QGIS histogram** on Band 3 to find good range

**Color Scheme:**
- **Rainbow:** Smooth gradients, best for most uses
- **Rainbow with Steps:** Discrete levels, good for classification

## Common Wavelength Ranges

### Minerals
- **Kaolinite:** 2160-2170 nm
- **Smectite:** 2200-2210 nm
- **Illite:** 2200-2220 nm
- **Muscovite:** 2190-2200 nm
- **Chlorite:** 2240-2260 nm
- **Calcite:** 2340 nm
- **Dolomite:** 2320 nm

### Vegetation
- **Chlorophyll:** 680 nm (red edge ~720 nm)
- **Water content:** 970, 1200, 1450, 1940, 2500 nm
- **Cellulose:** 2100 nm
- **Lignin:** 1730, 2270 nm

### Water
- **Pure water:** 970, 1200, 1450, 1940 nm
- **Ice:** Different depths at same wavelengths

## Troubleshooting

### "No wavelength information found"
**Problem:** Your image doesn't have wavelength metadata

**Solutions:**
1. Check if your image is truly hyperspectral (>10 bands)
2. For ENVI format: Ensure `.hdr` file has wavelength info
3. For GeoTIFF: Wavelengths may be in band descriptions
4. You may need to add wavelength metadata manually

### Output is all black
**Problem:** Depth range is wrong or no features detected

**Solutions:**
1. Check wavelength range matches your data
2. Adjust depth max (try 0.05, 0.1, 0.2, etc.)
3. Verify your data has absorption features in that range
4. Try wider wavelength range

### Colors don't match expected minerals
**Problem:** Wavelength range or data calibration issues

**Solutions:**
1. Verify wavelength range includes your target
2. Check if data is calibrated to reflectance
3. Compare with reference spectra
4. Adjust wavelength min/max for better contrast

### Map is too dark or too bright
**Problem:** Depth stretch needs adjustment

**Solutions:**
1. **Too dark:** Decrease max depth (0.2 → 0.1)
2. **Too bright:** Increase max depth (0.1 → 0.3)
3. Use QGIS histogram on depth band to find optimal range

## Advanced Workflows

### Multi-Feature Analysis
Run Wavelength of Minimum multiple times with different ranges:
1. 2000-2300 nm for clays
2. 2300-2500 nm for carbonates
3. 400-1000 nm for iron oxides

Then create separate maps for each.

### Change Detection
Run on time-series data:
1. Process each date separately
2. Compare wavelength positions over time
3. Look for shifts indicating change

### Integration with SAM
1. Run Wavelength of Minimum
2. Use wavelength positions to select ROIs
3. Extract spectra for SAM classification
4. Compare results

## Example Outputs

After running both tools, you'll have:

1. **wavelength_features.tif** (19 bands)
   - Scientific data for analysis
   - Bands 2-4 most useful
   - Can be used for statistics, time series, etc.

2. **wavelength_map.tif** (3 bands, RGB)
   - Visual representation
   - Easy to interpret
   - Great for presentations
   - Can overlay on imagery

## Next Steps

- Try different wavelength ranges for different materials
- Create a spectral library from identified features
- Use with SAM for classification
- Export results for reports and publications

---

**Questions?** See the full documentation in README.md and INSTALL.md

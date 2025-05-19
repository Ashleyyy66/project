# project

<a name="readme-top"></a>




### Creating a Benchmark: Normalized Difference Vegetation Index (NDVI)  
The NDVI is the most widely used indicator of live green vegetation, first introduced by Rouse et al. in 1973. It exploits the strong reflectance of healthy vegetation in the near-infrared and its absorption in the red wavelengths:

$$
\mathrm{NDVI} = \frac{\text{NIR} - \text{Red}}{\text{NIR} + \text{Red}}
$$
- **Sentinel-2 Bands:**  
  - **B04** → Red (665 nm, 10 m)  
  - **B08** → Near-Infrared (842 nm, 10 m)  
- **Typical Values:**  
  - **Dense vegetation:** NDVI > 0.5  
  - **Sparse vegetation or soil:** NDVI ≈ 0.2 – 0.5  
  - **Non-vegetated:** NDVI < 0.2  
- **Thresholding:**  
  In our Iowa AOI the NDVI histogram shows vegetation starting around **0.3**, so we adopt **NDVI > 0.30** to isolate green biomass.

---
### Creating a Benchmark: Normalized Difference Moisture Index (NDMI)  
The NDMI (sometimes called the Normalized Difference Water Index in SWIR) was introduced by Gao in 1996 to quantify vegetation and soil moisture by comparing NIR and SWIR:

$$
\mathrm{NDMI} = \frac{\text{NIR} - \text{SWIR}}{\text{NIR} + \text{SWIR}}
$$

- **Sentinel-2 Bands:**  
  - **B08** → Near-Infrared (842 nm, 10 m)  
  - **B11** → Short-Wave Infrared (1610 nm, 20 m)  
- **Typical Values:**  
  - **High moisture/healthy canopy:** NDMI > 0.3  
  - **Moderate moisture:** NDMI ≈ 0.0 – 0.3  
  - **Dry soil or bare surfaces:** NDMI < 0  
- **Thresholding:**  
  In our data the NDMI distribution is centered near zero, so we apply **NDMI > 0.00** to flag moist or densely vegetated areas.
  
---
### Creating a Benchmark: Normalized Difference Water Index (NDWI)  
The NDWI was first introduced by McFeeters (1996) to map open water bodies by comparing green and near-infrared reflectance. NDWI index is often used synonymously with the NDMI index as referenced above. Here we adopt the following convention:

$$
\text{NDWI} = \frac{\text{Green} - \text{NIR}}{\text{Green} + \text{NIR}}
$$

> **NDWI** = (Green – NIR)/(Green + NIR)  → water bodies  
> **NDMI** = (NIR   – SWIR)/(NIR   + SWIR) → canopy/soil moisture  

This separation ensures that NDWI highlights surface water, while NDMI highlights vegetation and soil moisture (Sentinel Hub, n.d.).


- **Sentinel-2 Bands:**  
  - **Green** = B03 (560 nm, 10 m)  
  - **NIR**   = B08 (842 nm, 10 m)  
- **Typical Values:**  
  - **Water bodies:** NDWI > 0.5  
  - **Vegetation:**  NDWI ≈ 0.0 – 0.2  
  - **Built-up/Soil:** NDWI ≈ –0.2 – 0.0  
- **Threshold for our Iowa AOI:**  
  The scene’s NDWI histogram is shifted toward lower values, so we apply **NDWI > 0.00** to reliably extract water features.

---
### Creating a Benchmark: Normalized Difference Built-Up Index (NDBI)  
The NDBI was proposed by Zha, Gao & Ni in 2003 to highlight urban and impervious surfaces, by contrasting SWIR reflectance (higher over concrete/asphalt) with NIR:

$$
\mathrm{NDBI} = \frac{\text{SWIR} - \text{NIR}}{\text{SWIR} + \text{NIR}}
$$

- **Sentinel-2 Bands:**  
  - **B08** → Near-Infrared (842 nm, 10 m)  
  - **B11** → Short-Wave Infrared (1610 nm, 20 m)  
- **Typical Values:**  
  - **Built-up areas:** NDBI > 0  
  - **Non-urban:** NDBI ≈ –0.2 – 0  
- **Thresholding:**  
  After up-sampling B11 to 10 m, the NDBI values in our scene cluster around zero, so we use **NDBI > 0.00** to detect built-up pixels.



**Data Source:**  
- **Sentinel-2 L2A** (10 m & 20 m bands) from Copernicus Data Space (via Google Earth Engine or direct download).  
- Core 10 m bands: B02 (Blue), B03 (Green), B04 (Red), B08 (NIR).  
- 20 m bands for indices: B05–B07 (red-edge), B8A (narrow NIR), B11–B12 (SWIR), plus the Scene Classification Layer (SCL) for cloud masking.

<!-- GETTING STARTED -->

## Getting Started

This project is built in **Google Colab**, but you can also run it locally in any Jupyter environment.

To get up and running:

1. **Download the notebook**  
   - Fetch `landuse_classification.ipynb` from this repository.

2. **Acquire the Sentinel-2 data**  
   - See **Datasets Used** below for details and filenames.
   - Download and unzip the `.SAFE` folder(s) to your local machine or Google Drive.

3. **Update file paths**  
   - In the Colab notebook, set the `main_path` variable to point at your unzipped  
     ```
     /path/to/S2A_MSIL2A_20250427T170711_N0511_R069_T15TVG_20250428T025712.SAFE/GRANULE/.../IMG_DATA/
     ```
   - If you use the same dataset, no other changes are required.  
   - If you substitute a different tile or date, update the filenames in the code cells accordingly.

4. **Run the notebook**  
   - Execute cells **in order**.  
   - Follow the inline instructions to compute indices, generate masks, and train models.



## Datasets Used

- **Sentinel-2 L2A (Surface Reflectance)**  
  - **AOI:** Central Iowa farmland (Tile **T15TVG**)  
  - **Date:** 2025-04-27  
  - **Bands:**  
    - 10 m: B02 (Blue), B03 (Green), B04 (Red), B08 (NIR)  
    - 20 m: B05–B07 (red-edge), B8A (narrow NIR), B11–B12 (SWIR), plus the Scene Classification Layer (SCL)

This agricultural region was selected because it features a mix of water bodies, cropland, sparse vegetation and built-up areas—perfect for testing multi-class land-use classification.



## Downloading Datasets

The Sentinel-2 `.SAFE` files are too large to include here. To download:

1. Go to the [Copernicus Data Space Browser](https://dataspace.copernicus.eu/) (free account required).  
2. In the **Search** tab, enter the filename:
- `S2A_MSIL2A_20250427T170711_N0511_R069_T15TVG_20250428T025712.SAFE`
3. Download the entire folder and **unzip** it.  
4. Place the unzipped folder in your workspace or Google Drive, then point `main_path` in the notebook to its `IMG_DATA` directory.

Once your data are in place and paths updated, simply run through the notebook cells to reproduce the analysis and classification results.

<p align="right">(<a href="#readme-top">back to top</a>)</p>


<!-- CONTACT -->
# Contact

Boxuan Li - [zcqsbli@ucl.ac.uk](mailto:zcqsbli@ucl.ac.uk) 

Project Link: [https://github.com/captainbluebear/GEOL0069-ML-Inland-Water-Body-Detection](https://github.com/Ashleyyy66/project)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- ACKNOWLEDGMENTS -->
# Acknowledgments
This project was created for GEOL0069 at University College London, taught by Dr. Michel Tsamados and Weibin Chen.

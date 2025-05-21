<h1 align="centre">Land use and Land Cover Classification with Sentinel-2 Imagery</h1>

<a name="readme-top"></a>

<!-- PROJECT LOGO -->
![image](https://github.com/user-attachments/assets/e786daee-21db-47d2-8a48-621d323f9f6f)

## Short Description

**Course Context:**  
This work was carried out for the **GEOL0069: Artificial Intelligence for Earth Observation** module at UCL.


**Background:**  
Accurate maps of land use and land cover (LULC) are essential for environmental planning, agricultural management, and water-resource monitoring. Satellite imagery—especially from the European Space Agency’s Sentinel-2 sensors—provides high-resolution, multi-spectral data for large-scale LULC mapping. 

**Problem Statement:**  
In central Iowa, seasonal vegetation cycles and narrow river channels challenge automated classification: simple index thresholds can misclassify bare fields and often miss small streams in early-season imagery.

**Objective:**  
Generate a detailed LULC map of Iowa’s agricultural core by differentiating water bodies, vegetation, urban areas, and bare land.

**Scope & Approach:**  
- **Data:** Sentinel-2 Level-2A imagery (spring 2025) over an Iowa AOI  
- **Indices:** Compute NDVI, NDWI, NDMI, and NDBI  
- **Methods:**  
  1. Rule-based thresholding of each index  
  2. Unsupervised clustering via K-Means  
- **Comparison:** Evaluate the strengths and limitations of each approach in a midwestern agricultural landscape


###  Normalized Difference Vegetation Index (NDVI)  
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
###  Normalized Difference Moisture Index (NDMI)  
The NDMI (sometimes called the Normalized Difference Water Index in SWIR) was introduced by Gao in 1996 to quantify vegetation and soil moisture by comparing NIR and SWIR:

$$
\mathrm{NDMI} = \frac{\text{NIR} - \text{SWIR}}{\text{NIR} + \text{SWIR}}
$$

<p align="right">(U.S. Geological Survey, n.d.)</p>

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
### Normalized Difference Water Index (NDWI)  
The NDWI was first introduced by Xu (2006) to map open water bodies by comparing green and near-infrared reflectance. NDWI index is often used synonymously with the NDMI index as referenced above. Here we adopt the following convention:

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
### Normalized Difference Built-Up Index (NDBI)  
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

## The Sentinel-2 Satellite
The Sentinel-2 Multispectral Instrument (MSI) consists of two satellites (L2A and L2B), with each MSI acquires data in 13 spectral bands spanning the visible, near-infrared and short-wave infrared (SWIR) regions (443–2190 nm), at three spatial resolutions (10 m, 20 m and 60 m) (Drusch et al., 2012). The four 10 m bands (Blue, Green, Red, and Near-Infrared) provide the highest spatial detail available for free, making Sentinel-2 ideal for fine-scale mapping (Abdi, 2019), while the 20 m red-edge and SWIR bands enhance discrimination of vegetation stress and built-up areas, and the 60 m bands are devoted to cloud and aerosol screening. 




<div align="center">
  <div style="display:inline-block; text-align:center; margin-right:20px;">
    <img src="https://github.com/user-attachments/assets/9cf37022-4a35-4b03-a80d-bbe88964fed4" alt="Remote sensing schematic" width="400" />
    <div><strong>(a)</strong></div>
  </div>
  <div style="display:inline-block; text-align:center;">
    <img src="https://github.com/user-attachments/assets/c594b33e-0259-4a16-9add-10f99214c8f0" alt="NDVI conceptual illustration" width="400" />
    <div><strong>(b)</strong></div>
  </div>
</div>

**Figure 2.** **(a)** Remote sensing schematic. **(b)** NDVI conceptual illustration, adapted from *Remote-sensing technology* (n.d.) by Elsevier. Retrieved May 21, 2025, from [https://doi.org/10.1016/j.rse.2011.11.026](https://doi.org/10.1016/j.rse.2011.11.026)



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

Central Iowa was selected because it features a mix of water bodies, cropland, sparse vegetation and built-up areas—perfect for testing multi-class land-use classification.



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

## References
Abdi, A. M. (2019). Land cover and land use classification performance of machine learning algorithms in a boreal landscape using Sentinel-2 data. GIScience & Remote Sensing, 57(1), 1–20. https://doi.org/10.1080/15481603.2019.1650447

Drusch, M., Del Bello, U., Carlier, S., Colin, O., Fernandez, V., Gascon, F., Hoersch, B., Isola, C., Laberinti, P., Martimort, P., Meygret, A., Morin, D., Sy, O., & Marchese, F. (2012). Sentinel-2: ESA’s Optical High-Resolution Mission for GMES Operational Services. *Remote Sensing of Environment, 120*, 25–36. https://doi.org/10.1016/j.rse.2011.11.026


Xu, H. (2006). Modification of normalised difference water index (NDWI) to enhance open water features in remotely sensed imagery. *International Journal of Remote Sensing*, *27*(14), 3025–3033. https://doi.org/10.1080/01431160600589179

Zha, Y., Gao, J., & Ni, S. (2003). Use of Normalized Difference Built-up Index in automatically mapping urban areas from TM imagery. *International Journal of Remote Sensing*, *24*(3), 583–594. https://doi.org/10.1080/01431160304987

Rouse, J. W., Haas, R. H., Deering, D. W., & Schell, J. A. (1973). *Monitoring the vernal advancement and retrogradation (green wave effect) of natural vegetation* (NASA Technical Report No. RSC-1973-1, pp. 44–47). College Station, TX: Remote Sensing Center, Texas A&M University.

Sentinel Hub. (n.d.). *Normalized Difference Moisture Index (NDMI) – Sentinel-2 custom script*. Retrieved May 20, 2025, from https://custom-scripts.sentinel-hub.com/custom-scripts/sentinel-2/ndmi/

U.S. Geological Survey. (n.d.). *Normalized Difference Moisture Index (NDMI)*. Retrieved May 20, 2025, from https://www.usgs.gov/landsat-missions/normalized-difference-moisture-index

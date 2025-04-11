# WetlandMapping_Toolkit
## Mapping Drier-End Wetlands Using Land Use/Land Cover (LULC) Classifications

This repository contains Google Earth Engine (GEE) and Python code for drier-end wetland mapping:

Authors: **Migone, L., Schivo, F., Verón, S., Banchero, S., and Grimson, R. (2025)** Pending publication.

## Repository Overview

### GEE Code
This repository contains two Google Earth Engine (GEE) scripts: **S1-preproc-Wetland-LULC** and **Annual-Wetland-LULC**. Both scripts generate annual and seasonal features for a single year, with the default period running from June 21st (the start of winter in the Southern Hemisphere) to June 20th of the following year (the end of autumn). Each script should be run once per year to generate its Land Use/Land Cover (LULC) classification.

#### S1-preproc-Wetland-RF-LULC
This script preprocesses Sentinel-1 imagery by applying:
- **Border noise correction**
- **Speckle filtering**
- **Radiometric terrain normalization**  

It then derives the following features from VV, VH, and VV/VH bands:
- Seasonal means
- Annual means and standard deviation  

The processed image, containing all the generated bands, is exported to GEE Assets for later use in **wetland-RF-LULC**. This separation prevents exceeding GEE’s free processing memory limits.

#### wetland-RF-LULC
This script generates the LULC classification using a **Random Forest classifier**.

1. **Input Features:**
   - **Topography-derived features**: Generated externally and imported as GEE Assets.  
     - The sample script includes the **Topographic Position Index (TPI)** from a LiDAR DEM, computed with 200m and 500m radii (TPI200 and TPI500).  
     - It also computes **GLCM texture descriptors**: sum average (savg), variance (var), and contrast.
   - **Sentinel-1 Derived Features**: Preprocessed with **S1-preproc-Wetland-RF-LULC**.
   - **Sentinel-2 Derived Features**: Obtained from the GEE repository.  
     - The script computes the following spectral indices: **NDVI, NDWI, CVI, MNDWI1, MNDWI2, and SAVI**.  
     - It extracts **seasonal and annual reducers** (mean and standard deviation) of spectral bands and indices.  
     - It computes **GLCM texture descriptors** (savg, var, contrast) on selected layers.

2. **Training and Test Points:**
   - **Initial Training and Test Points**: Must be uploaded and imported from GEE Assets. The methodology for generating them is user-defined.
   - **Complementary Training Points**: Generated manually through visual interpretation of the LULC classification and used iteratively to correct classification errors until achieving acceptable accuracy.

3. **Output Files:**  
   The script exports results to a Google Drive output folder, generating:
   - **GeoTIFF file** – Final LULC classification.
   - **CSV file** – Feature importance values from the Random Forest model.

### Python Code
The Python script processes a series of wetland-oriented LULC classifications to generate a final wetlands map.

**NOTE 1**: This script is set to work with **8 LULC classifications (8 years classified)**, each containing **8 LULC classes**. However, it can be adapted for a different number of years or classes.  

**NOTE 2**: Due to the high RAM usage of some steps, it is recommended **not to run the entire script at once**, but rather in stages (as suggested in comments within the script).

1. **Input Layers:**
   - A series of **LULC classifications** stored as GeoTIFF files (which may be generated with the GEE code).
   - A **GeoJSON file** containing the delineation of river channels.

2. **Labeling Considerations:**
   - The script reads LULC classifications from a **single directory**, which should contain only classification GeoTIFF files.
   - The script generates a series of raster and vector images as **final or intermediate products**. To organize them, it creates structured output directories.
   - All output file names include:
     - A **name prefix** (`fn_prefix`)
     - A reference to the **years covered** (e.g., `_16to23`)
     - A **running date** (`running_date`)

3. **Filtering Parameter Considerations:**
   - These parameters can be adapted to better fit the characteristics or requirements of the landscape where the classification is applied.

4. **LULC Classes Considerations:**
   - LULC classes should be identified by **integers** (e.g., Class "Crops" = 5). Any integer will work, but ensure that the class definitions are clearly documented within the script.
   - The script is designed to work with:
     - **3 wetland classes**
     - **1 woody vegetation class**
     - **4 non-wetland classes**  
     It can handle more non-wetland classes, but modifications are needed if the number of wetland classes changes.
   - The script is designed to use an **input layer containing river channel delineation** and to consider **woody areas around river channels as wetlands**. This step can be disabled if required.

## Installation and Dependencies
To use the scripts in this repository, ensure you have the following dependencies installed:

### **GEE Scripts**
- Google Earth Engine account and access to GEE Code Editor.

### **Python Script**
#### **Required Libraries**
Install required dependencies using:
- numpy
- geopandas
- scipy
- pandas
- rasterio
- skimage
- shapely

## How to Cite
If you use this repository in your research, please cite:

Pending publication.

## License
This repository is licensed under the GPL licence

## Acknowledgments
We acknowledge the support of Consejo Nacional de Investigaciones Científicas y Técnicas (CONICET) and Universidad Nacional de San Martín (UNSAM), to develop this repository.

## Contact
For questions or collaboration, please contact:
- Lucía Migone – lmigone@unsam.edu.ar
- GitHub: lmigone

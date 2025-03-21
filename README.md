# WetlandMapping_Toolkit
## Mapping drier-end wetlands using Land Use/Land Cover (LULC) classifications

This repository contains Google Earth Engine (GEE) and Python code for drier-end wetland mapping presented in:

Migone, L., Schivo, F., Verón, S. and Grimson, R. (2025). XXXXX

## Repository overview

### GEE code
This repository contains two Google Earth Engine (GEE) scripts: **S1-preproc-Wetland-RF-LULC** and **wetland-RF-LULC**. Both scripts generate annual and seasonal features for a single year, with the default period running from June 21st (the start of winter in the Southern Hemisphere) to June 20th of the following year (the end of autumn). Each script should be run once per year to generate its Land Use/Land Cover (LULC) classification.

#### S1-preproc-Wetland-RF-LULC
This script preprocesses Sentinel-1 imagery by: border noise correction, speckle filtering and radiometric terrain normalization. It then derives the following features from VV, VH, and VV/VH bands: seasonal means, and annual means and standard deviation. The processed image, containing all the generated bands, is exported to GEE Assets for later use in wetland-RF-LULC. This separation prevents exceeding GEE’s free processing memory limits.

#### wetland-RF-LULC
This script generates the LULC classification using a Random Forest classifier

1. Input features:
	- Topographic-derived features. These are generated externally and imported as GEE Assets.
   The sample script includes Topographic Position Index (TPI) from a LiDAR DEM, computed with 200m and 500m radii (TPI200 and TPI500). It computes GLCM texture descriptors: savg (sum average), var (variance) and contrast.   
	- Sentinel-1 Derived Features. Preprocessed with S1-preproc-Wetland-RF-LULC.
	- Sentinel-2 Derived Features. Sentinel 2 imagery is obtained from the GEE repository. The script computes the following spectral indices: NDVI, NDWI, CVI, MNDWI1, MNDWI2, SAVI. It extracts seasonal and annual reducers (mean and standard deviation) of spectral bands and indices. It Computes GLCM texture descriptors on selected layers (savg, var, contrast).

2. Training and Test Points:
	- Initial Train and Test Points. Must be uploaded and imported from GEE Assets. The methodology for generating them is user-defined.
	- Complementary Train Points. Generated manually through visual interpretation of the LULC classification, and used to correct classification errors. The process is iterative, repeated until achieving acceptable accuracy.

3. Output Files:
The script exports results to a Google Drive output folder, generating:
	- GeoTIFF file – Final LULC classification.
	- CSV file – Feature importance values from the Random Forest model.

### 2. Python code
The python code is a single script that contains the whole workflor to transform a series of wetland-oriented LULC classifications into a wetlands map. 

**NOTE 1**: this script is set to work with 8 LULC classifications (8 years classified), and each one has 8 LULC classes. However, it can be adapted for a different number of years or classes. 
**NOTE 2**: Do to how RAM -emory demanding some of the steps are, it is recomended not to run the whole script at once, but to do it by stages (as sugested in coments).

1. **Input** layers:

This script takes as input a series of LULC classifications stored on GeoTIFF files (may be generated with the GEE code), and a GeoJSON file with the delimitation of river channels.
2. **Labeling** considerations:
This script takes the LULC classifications from a single directory that sould only store the classifications as GeoTIFF files. 
This script generates a series of images that are final or intermediate products which are rester or vector files. To better store them, a series of directories are made.
All the images output file names include a name prefix (fn_prefix). They also include a reference to the years covered by the classification (years_covered; e.g. '_16to23'), and a running date (running_date).

3. **Filtering parameters** considerations: 





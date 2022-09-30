# yield_forecasting
<b>The focus of the Project/Model:</b>
Using the ground data from rice farms in the Katavi region of Tanzania, we build a machine learning model that predicts the end-of-season rice yield from different earth observation features.
Methods and datasets used
Field/Ground Data:
Data Source/Collection: The team collected field boundary and yield measurement data and other field characteristics from smallholder rice fields in North-Western Tanzania from January through June 2022. The Dataset contains farm ID,, location coordinates, region, geometry, area, the weight of the harvest, and other attributes that pertain to the field geography. We adopted the ODK standard toolkit developed by ECCAS in collaboration with Radiant Earth Foundation, and data collection was conducted through smartphones installed with ODK software. The project hired a Field Supervisor and eight (8) Field Surveyors and trained them in partnership with a technical team from the Sokoine University of Agriculture. The first survey on field boundaries started at the beginning of the rice production season and ultimately reached 806 farmers. The second survey was more challenging as the surveyor needed to reach the farm at the exact harvest time to ensure that the full harvest quantity could be measured. The surveyors were in constant contact with the farmers from the first survey, so the harvest date was noted and with few exceptions, the surveyors were present at the time of harvest for the second survey, completing a total of 617 yield measurements. 

Test Data Boundaries: A shapefile was created to represent the geographical boundaries of each surveyed field. A GPS survey was used on-site to calculate the coordinates of the field's boundaries. This data was then converted into Geometry data in Python. These initial polygons underwent digitization before yield and area were calculated. The GEOCIF data for January through June comes with multiple features that provide information about the field’s location, area, geometry, and harvest. Given a shapefile with a polygon for each field and an attribute for the weight of the harvest, we calculate a yield value for each field. Geopandas is used to convert the file to the UTM Zone 36S coordinate reference system, and then the area is calculated for each polygon in square meters. Geopandas also allows yield calculation by dividing the weight by the area in a new attribute.


Data Visualization:

Figure 1: The green area represents rice farms located in Katavi.Administrative level 2 borders are shown on the map. 
Satellite Data
Crop Mask Generation
A single seasonal mosaic was created from Level-2A Sentinel-2 imagery in Google Earth Engine (GEE), excluding cloudy and shadow pixels, using the Sentinel2Cloud probability masks. The median pixel mosaic was then clipped to the area of interest. Only the 10 meter (Bands 2, 3, 4, and 8) and 20 meters (Bands 5, 6, 7, 8A, 11, and 12) were used for this analysis. The 20-meter bands were pan-sharpened to 10 meters before training the crop-type classifier. An  NDVI band was added to the seasonal mosaic. NDVI data are a common data source for land cover mapping and have been shown to improve model accuracy when added as an input variable. Random Forests (RF) model was selected after several experiments, including running the support vector machine and Regression Trees and Random Forest all readily accessible in GEE. RF had the highest overall accuracy.  
Yield Mapping 
Multiple time-series satellite datasets utilized for this task were imported from GEE. For this model, the Sentinel-2A image collection has been imported. The shapefile for the region of interest (Katavi, administrative level 1) was added as a GEE asset. The time series image collection was filtered to cover only the region enveloped by the shapefiles from January to June 2022.
Features:
Normalized  Difference Vegetation Index: 
“Normalized Difference Vegetation Index (NDVI) quantifies vegetation by measuring the difference between near-infrared (which vegetation strongly reflects) and red light (which vegetation absorbs).” But when NDVI is close to zero, there are likely no green leaves, and it could even be a built-up/ impervious area.

NDVI = (NIR - Red)(NIR + Red)

Equation 1: Calculation for NDVI from Sentinel 2 bands 8 and 4






Monthly NDVI Features

Figure 2: Maps of NDVI max and median for each month of the rice growing season evaluated in the survey

Green Chlorophyll Vegetation Index:
The Chlorophyll Index - Green (Clg) is a vegetation index used to estimate leaf chlorophyll content in the plants based on near-infrared and green bands. In general, the chlorophyll value directly reflects the vegetation.

        GCVI =NIRGreen -1 
Equation 2: Calculation for GCVI from Sentinel 2 bands 8 and 3

Sentinel-2 derived NDVI and GVCI are extracted for the area of interest biweekly from January through June from Google Earth Engine.

Extraction of NDVI time series data:
The Sentinel-2 Image Collection provides 13 bands of data. Bands - B4 (Red) and B8  (NIR) are selected for each image. The normalizedDifference() function is then mapped over Red and NIR band data to derive the NDVI values. We apply the maxreducer() on the NDVI band images to obtain maxNDVI values. The NDVI tif images are extracted  at 10m resolution.

We generate these values every two weeks between the date range and export these images into a tiff file. These files can be accessed in Python using Geopandas/Rasterio and can be visualized. 

Extraction of GCVI time series data:
Sentinel Image Collection provides 13 bands of data. We select Bands - B3 (Green) and B8 (NIR) for each image. We then map the function which calculates GCVI using the mathematical expression on green and NIR band data to derive the NDVI values. We apply the maxreducer() on the NDVI band images to obtain maxNDVI values. 

We generate these values every two weeks between the date range and export these images into a tiff file. We extract the images at 10m resolution. These files can be accessed in Python using Geopandas / Rasterio and can be visualized. 


Figure 3: Plot of raster containing max GCVI values over the two weeks 4/9/2022 - 4/23/2022

Building the Regional Model:
Machine Learning using PyspatialML and Scikit-learn
With the python library Pyspatialml, scikit-learn machine learning models can be applied to raster-based datasets. Here we used monthly maximum NDVI rasters extracted from the GEE products as bands with the tsraster library as input features. The target data, field-scale yield values, were in a shapefile format that could be overlaid on the rasters to extract corresponding feature raster values to create the training data set. Pyspatialml also includes methods to plot the results and modify rasters.
Time Series NDVI:


Figure 4: NDVI time series plot, NDVI value at each biweekly interval represents an average of the values across rice fields locations
Model Experimentation:
NDVI Data Modeling:

The preprocessed data was run through multiple prediction models to compare and analyze the results, performance metrics, and visualization. The regression models assessed are shown in figure 5 below showing model predicted yield values against the Maximum Bi-weekly NDVI values during June.


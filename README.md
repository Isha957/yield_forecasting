# yield_forecasting
<html>
<body>
<p style="font-family:times" >
        <b>The focus of the Project/Model:</b></br>
Using the ground data from rice farms in the Katavi region of Tanzania, we build a machine learning model that predicts the end-of-season rice yield from different earth observation features.
        <p color:"009B77"><b>Test Data Boundaries</b></p></br>
A shapefile was created to represent the geographical boundaries of each surveyed field.  The GEOCIF data for January through June comes with multiple features that provide information about the field’s location, area, geometry, and harvest. Given a shapefile with a polygon for each field and an attribute for the weight of the harvest, we calculate a yield value for each field. Geopandas is used to convert the file to the UTM Zone 36S coordinate reference system, and then the area is calculated for each polygon in square meters. Geopandas also allows yield calculation by dividing the weight by the area in a new attribute.


<b>Crop Mask Generation:</b></br>
A single seasonal mosaic was created from Level-2A Sentinel-2 imagery in Google Earth Engine (GEE), excluding cloudy and shadow pixels, using the Sentinel2Cloud probability masks.


<b>Yield Mapping</b></br>
Multiple time-series satellite datasets utilized for this task were imported from GEE. For this model, the Sentinel-2A image collection has been imported. The shapefile for the region of interest (Katavi, administrative level 1) was added as a GEE asset. The time series image collection was filtered to cover only the region enveloped by the shapefiles from January to June 2022.

<b>Features:</b></br>
<b>Normalized  Difference Vegetation Index:</b></br> 
“Normalized Difference Vegetation Index (NDVI) quantifies vegetation by measuring the difference between near-infrared (which vegetation strongly reflects) and red light (which vegetation absorbs).” But when NDVI is close to zero, there are likely no green leaves, and it could even be a built-up/ impervious area.

        NDVI = (NIR - Red)(NIR + Red)
Equation 1: Calculation for NDVI from Sentinel 2 bands 8 and 4

<b>Green Chlorophyll Vegetation Index:</b></br>
The Chlorophyll Index - Green (Clg) is a vegetation index used to estimate leaf chlorophyll content in the plants based on near-infrared and green bands. In general, the chlorophyll value directly reflects the vegetation.

        GCVI =NIRGreen -1 
Equation 2: Calculation for GCVI from Sentinel 2 bands 8 and 3

Sentinel-2 derived NDVI and GVCI are extracted for the area of interest biweekly from January through June from Google Earth Engine.
<ul>
<li><b><i>Extraction of NDVI time series data:</i></b></li>
<p>Using the Sentinel-2 Image Collection band data: Bands - B4 (Red) ; B8  (NIR) and the above formula, we obtain max NDVI values for each image. The NDVI tif        images are extracted  at 10m resolution.We generate these values every two weeks between the date range and export these images into a tiff file. These files can be accessed in Python using Geopandas/Rasterio and can be visualized.</p>

<li><b><i>Extraction of GCVI time series data:</i></b></li>
<p>Using the Sentinel-2 Image Collection band data: Bands - B3 (Green) ; B8  (NIR) and the above formula, we obtain max GCVI values for each image. The GCVI        tif images are extracted  at 10m resolution.We generate these values every two weeks between the date range and export these images into a tiff file. These files can be accessed in Python using Geopandas/Rasterio and can be visualized.
</ul>

<b><u>Building the Regional Model:</u></b>
Machine Learning using PyspatialML and Scikit-learn
With the python library Pyspatialml, scikit-learn machine learning models can be applied to raster-based datasets. Here we used monthly maximum NDVI rasters extracted from the GEE products as bands with the tsraster library as input features. The target data, field-scale yield values, were in a shapefile format that could be overlaid on the rasters to extract corresponding feature raster values to create the training data set. Pyspatialml also includes methods to plot the results and modify rasters.

<b><u>Model Experimentation:</u></b>
<ul>
<li><b><i>NDVI Data Modeling:</i></b></li>
<p>The preprocessed data was run through multiple prediction models to compare and analyze the results, performance metrics, and visualization. The regression        models assessed to show model predicted yield values against the Maximum Bi-weekly NDVI values during June.</p>

<li><b><i>GCVI Data Modeling:</i></b></li>
<p>The preprocessed data was run through multiple prediction models to compare and analyze the results, performance metrics, and visualization. The regression      models assessed to show model predicted yield values against the Maximum Bi-weekly GCVI values during June.</p>
</p>
        
</body>
</html>

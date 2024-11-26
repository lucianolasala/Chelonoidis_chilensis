### Google Earth Engine:  una plataforma de cómputo en la nube para análisis de datos de observación de la tierra y agua a escala global. 

Clould computing using Google Earth Engine (https://earthengine.google.com/) is described for each of the variables included in model calibration and projection. For the present work, two different assets (i.e. calibration and projection areas) were defined using vector files (ESRI shapefiles). All the analyses were implemented using the Earth Engine API available in JavaScript. The entire set of original variables is described, of which after the correlation analysis only a subset remained for the modeling purposes.    

##### Table 1. Elevacion, clima y vegetación 

|Variable          |Banda           |Reductor      |Resol. espacial|Resol. temporal |GEE snippet<sup>*</sup> |   
|------------------|-----------------|-------------| --------------|--------------- |----------------------- |
|DEM               |elevation        |NA           |90 m           |NA              |CGIAR/SRTM90_V4         |
|Vegetación ind.   |EVI              |Mean         |1 km           |2000-2020       |MODIS/006/MOD13A2       |    |Precip. global    |precipitationCal |Anual mean   |0.1 deg.       |2000-2021	      |NASA/GPM_L3/IMERG_V07   |    
|Temp. diurna      |LST_Day_1km      |Mean         |1 km           |2000-2021       |MODIS/006/MOD11A1       |
|                  |                 |Min.         |1 km           |2000-2021       |MODIS/006/MOD11A1       |
|                  |                 |Max.         |1 km           |2000-2021       |MODIS/006/MOD11A1       |
|Temp. nocturna    |LST_Night_1km    |Mean         |1 km           |2000-2021       |MODIS/006/MOD11A1       |
|                  |                 |Min.         |1 km           |2000-2021       |MODIS/006/MOD11A1       |
|                  |                 |Max.         |1 km           |2000-2021       |MODIS/006/MOD11A1       |
|Prod. prim. bruta |GPP              |Mean         |500 m          |2002-2017       |CAS/IGSNRR/PML/V2       |
|Intercept. dorsel |Ei               |Mean         |500 m          |2002-2017       |CAS/IGSNRR/PML/V2       |
|Transp. suelo     |Es               |Mean         |500 m          |2002-2017       |CAS/IGSNRR/PML/V2       |
|Transp. vegetación|Ec               |Mean         |500 m          |2002-2017       |CAS/IGSNRR/PML/V2       |
|Cobertura suelo   |LC_Type1         |Mean         |500 m          |2021            |MODIS/061/MCD12Q1       |

***
<sup>*</sup>GEE (Google Earth Engine) collection snippets provide direct reference to data sources.  
 
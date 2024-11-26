### Google Earth Engine:  una plataforma de cómputo en la nube para análisis de datos de observación de la tierra y agua a escala global. 

En la tabla al pie se describen las caracetrísticas principales de las variables incluidas en durante la calibración y proyección de modelos, las cuales fueron procesadas en Google Earth Engine (https://earthengine.google.com/), utilizando de su JavaScript API. En el presente trabajo, se utilizaron dos archivos vectoriales diferentes (ESRI shapefiles procesados como "assets"), uno para el área de calibración y otro para el área de proyección del modelo. De las variables presentadas, solo un subset fue seleccionado para inslución en el modelado luego de realizar un análisis de correlación entre las mismas.   

##### Tabla. Elevacion, clima y vegetación 

|Variable          |Banda           |Reductor      |Resol. espacial|Resol. temporal |GEE snippet<sup>*</sup> |   
|------------------|-----------------|-------------| --------------|--------------- |----------------------- |
|DEM               |elevation        |NA           |90 m           |NA              |CGIAR/SRTM90_V4         |
|Vegetación        |EVI              |Mean         |1 km           |2000-2020       |MODIS/006/MOD13A2       |    |Precipitación     |precipitationCal |Anual mean   |0.1 deg.       |2000-2021	      |NASA/GPM_L3/IMERG_V07   |    
|Temp. diurna      |LST_Day_1km      |Mean         |1 km           |2000-2021       |MODIS/006/MOD11A1       |
|                  |                 |Min.         |1 km           |2000-2021       |MODIS/006/MOD11A1       |
|                  |                 |Max.         |1 km           |2000-2021       |MODIS/006/MOD11A1       |
|Temp. nocturna    |LST_Night_1km    |Mean         |1 km           |2000-2021       |MODIS/006/MOD11A1       |
|                  |                 |Min.         |1 km           |2000-2021       |MODIS/006/MOD11A1       |
|                  |                 |Max.         |1 km           |2000-2021       |MODIS/006/MOD11A1       |
|Intercept. dorsel |Ei               |Mean         |500 m          |2002-2017       |CAS/IGSNRR/PML/V2       |
|Transp. suelo     |Es               |Mean         |500 m          |2002-2017       |CAS/IGSNRR/PML/V2       |
|Transp. vegetación|Ec               |Mean         |500 m          |2002-2017       |CAS/IGSNRR/PML/V2       |
|Cobertura suelo   |LC_Type1         |Mean         |500 m          |2021            |MODIS/061/MCD12Q1       |

***
<sup>*</sup>GEE (Google Earth Engine) collection snippets provide direct reference to data sources.  
 
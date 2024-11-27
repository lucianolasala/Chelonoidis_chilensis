### Análisis de idoneidad ambiental para **Chelonoidis chilensis ** en ecorregiones de Argentina

#### El código que sigue fue utilizado para realizar análisis estadísticos descriptivos de idoneidad ambiental a nivel de ecorregiones de Argentina.  

```r
library(sp)
library(sf)        
library(raster)    
library(magrittr) 
library(dplyr)     
library(stringr)  
library(rgeos)
library(rgdal)
library(xlsx)	
```


#### Carga y unión de rasters del modelo: área de calibración y de proyección 

```r
cal = raster("D:/CIC/Analisis/C_chilensis_EMN/Modelado/Ciclo_2/Final_model_stats/Statistics_E/calibration_mean.tif")
proj = raster("D:/CIC/Analisis/C_chilensis_EMN/Modelado/Ciclo_2/Final_model_stats/Statistics_E/Current_mean.tif")
ras = merge(cal, proj)

writeRaster(ras, "D:/CIC/Analisis/C_chilensis_EMN/Final_model_rasters/Final_model_mean.tif")

mosaico <- mosaic(cal, proj, fun = "mean")
plot(mosaico)

# Crop and mask model raster to Argentina's borders
r <- raster("D:/CIC/Analisis/C_chilensis_EMN/Final_model_rasters/Final_model_mean.tif")
arg <- st_read("D:/CIC/Analisis/C_chilensis_EMN/Vectores/Countries/ARG_adm/ARG_adm0.shp")

r_arg <- crop(r, arg) %>% mask(arg); plot(r_arg)
writeRaster(r_arg, "D:/CIC/Analisis/C_chilensis_EMN/Final_model_outputs/Final_model_mean_arg.tif")
```

#### Loading vector file for all ecoregions

ecoregiones <- st_read("D:/CIC/Analisis/C_chilensis_EMN/Vectores/Ecoregions/Ecos_Argentina.shp")
eco_names <- unique(ecoregiones$ECO_NAME); eco_names

# Function to iterate through all ecoregions, calculating the necessary 
# statistics and saving as plain text file.

procesado <- function(nombre_eco){
  eco = ecoregiones[ecoregiones$ECO_NAME == nombre_eco,]
  eco_mask <- crop(ras, eco) %>% mask(eco)
  
  eco_mean = cellStats(eco_mask, stat = "mean", na.rm = TRUE, asSample = TRUE)
  eco_min = cellStats(eco_mask, stat = "min", na.rm = TRUE, asSample = TRUE)
  eco_max = cellStats(eco_mask, stat = "max", na.rm = TRUE, asSample = TRUE)
  eco_sd = cellStats(eco_mask, stat = "sd", na.rm = TRUE, asSample = TRUE)
  return(c(eco_mean, eco_min, eco_max, eco_sd))
}

eco_means <- c()
eco_mins <- c()
eco_maxs <- c()
eco_sds <- c()

for(i in eco_names){
  a <- procesado(i)
  eco_means <- append(eco_means, a[1])
  eco_mins <- append(eco_mins, a[2])
  eco_maxs <- append(eco_maxs, a[3])
  eco_sds <- append(eco_sds, a[4])
}

res <- data.frame(eco_names, eco_means, eco_mins, eco_maxs, eco_sds) %>% 
  mutate(across(where(is.numeric), ~ round(., 3)))

res <- res[order(res$eco_means, decreasing = T), ]

res_unique <- res[!duplicated(res[1]), ]

length(res_unique$eco_names)

write.csv(res_unique, "D:/CIC/Analisis/C_chilensis_EMN/Final_model_outputs/Ecos_summary_table.csv")
write.xlsx(res_unique, file = "D:/CIC/Analisis/C_chilensis_EMN/Final_model_outputs/Ecos_summary_table.xls", sheetName = "Sheet1", 
           colNames = TRUE, rowNames = TRUE, append = FALSE)


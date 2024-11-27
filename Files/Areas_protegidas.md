### Procesamiento de datos de áreas protegidas

#### El presente código se utilizó para acceder a la base de datos Protected Planet (https://www.protectedplanet.net/en), corregir geometrías inválidas, filtrar y descargar archivos vectoriales.

#### Paquetes y librerías 

```r
install.packages("remotes")
remotes::install_github("luisdva/annotater")
install.packages("wdpar", repos = "https://cran.rstudio.com/")
install.packages("netstat")  
install.packages("ggmap")
install.packages("prepr")  # not available (for R version 4.0.2)

library(remotes)  # install packages from remote repositories or sources beyond CRAN
library(wdpar)  # Interface to the World Database on Protected Areas 
library(dplyr)  # A Grammar of Data Manipulation 
library(magrittr)  # A Forward-Pipe Operator for R
library(sf)  # Simple Features for R
library(prepr)  # Automatic Repair of Single Polygons
library(netstat)  # Retrieve network-related statistics and manage TCP ports

# Side note: if error message "Error in wdman::phantomjs(verbose = FALSE) : 
# PhantomJS signals port = 4567 is already in use" appears, restart R session to free port
```

#### Procesamiento

```r
arg_pa <- wdpa_fetch("ARG", wait = TRUE, download_dir = "D:/CIC/Analisis/C_chilensis_EMN/Vectores/PPAA/WDPA")
plot(arg_pa$geometry)

arg_pa <- wdpa_fetch("ARG", wait = TRUE, download_dir = tempdir())
class(arg_pa)

# Clean the data: briefly, the cleaning steps include: excluding protected areas that   # are not yet implemented, excluding protected areas with limited conservation 
# value, replacing missing data codes (e.g. "0") with missing data values (i.e. NA),    # replacing protected areas represented as points with circular protected areas that    # correspond to their reported extent, repairing any topological issues with the        # geometries, and erasing overlapping areas.

arg_pa_clean <- wdpar::wdpa_clean(arg_pa, erase_overlaps = FALSE)
class(arg_pa_clean)
head(arg_pa_clean)

type <- as.data.frame(arg_pa_clean$MARINE)
head(type)
summary(type)  #  marine: 21; partial: 22; terrestrial: 406

unique(arg_pa_clean$MARINE)  # "terrestrial" "marine" "partial"  # Codes 0, 1 and 2 in shapefile
st_crs(arg_pa_clean)  # WGS84

arg_pa_clean <- st_transform(arg_pa_clean, crs = st_crs(arg_pa))
st_crs(arg_pa_clean)

st_write(arg_pa_clean, "C:/Users/User/Documents/Analyses/Wild boar ENM/Vectors/AP_Arg/Arg_PA_clean.shp")

# Subset terrestrial and partial protected areas and generate/save a list
arg_pa_clean <-
    arg_pa_clean %>%
    filter(MARINE == "terrestrial" | MARINE == "partial")

# Reproject
arg_pa_clean <- st_transform(arg_pa_clean, crs = st_crs(arg_pa))
st_write(arg_pa_clean, "D:/CIC/Analisis/C_chilensis_EMN/Vectores/PPAA/WDPA/Arg_PA_final.shp")
```
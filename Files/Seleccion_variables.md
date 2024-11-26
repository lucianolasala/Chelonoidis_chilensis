### Análisis de correlación y selección de variables ambientales

```r
gc()
rm(list=ls(all=TRUE))

install.packages("openxlsx")

library(tidyverse) # Easily Install and Load the 'Tidyverse'
library(sf) # Simple Features for R
library(stars) # Spatiotemporal Arrays, Raster and Vector Data Cubes
library(magrittr) # A Forward-Pipe Operator for R
library(raster) # Geographic Data Analysis and Modeling
library(writexl) # Read, Write, Format Excel 2007 and Excel 97/2000/XP/2003 Files
library(corrplot) # Visualization of a Correlation Matrix
library(ggcorrplot) # Visualization of a Correlation Matrix using 'ggplot2'
library(openxlsx)
```

#### Verificación de resolución espacial y extensión de rasters para área de calibración (M)

```r
path = ("D:/LFLS/Analyses/C_chilensis_EMN/Rasters_GEE_asc/M") 

files <- list.files(path = path, pattern = ".asc$", full.names = TRUE); files  
mytable <- NULL

for(i in 1:length(files)){
  r <- raster(files[i])
  mytable <- rbind(mytable, c(files[i], round(c(res(r), as.vector(extent(r))), 16)))
}

colnames(mytable) <- c("File","Resol.x","Resol.y","xmin","xmax","ymin","ymax")
class(mytable)
mytable <- as.data.frame(mytable)

write_xlsx(mytable, "D:/LFLS/Analyses/C_chilensis_EMN/Correlations/Rasters_props1.xlsx")
```

#### Correlación

```r
rm(list=ls(all=TRUE))

path = ("D:/CIC/Analisis/C_chilensis_EMN/Rasters_GEE_asc/M")
files <- list.files(path = path, pattern = ".asc$", full.names = T)
files

mystack <- stack(files)
class(mystack)
dim(mystack) 
mystack@layers 

k <- which(!is.na(mystack[[1]][]))
class(k)
is.vector(k)
length(k)  

n.samp = round(length(k)*0.1, 0)
k.samp <- sample(k, size = n.samp)
length(k.samp) 
class(k.samp)
head(k.samp)

k.final <- raster::extract(mystack, k.samp)

class(k.final)   
dim(k.final) 

length(which(is.na(k.final)))  

cor.matrix <- cor(k.final, use = "pairwise.complete.obs", method = "pearson")  
head(cor.matrix)
dim(cor.matrix)  
is.matrix(cor.matrix) 

write.table(cor.matrix, "D:/LFLS/Analyses/C_chilensis_EMN/Correlations/Cor_matrix1.csv", row.names=FALSE, col.names=FALSE, sep = ',')
write.csv(cor.matrix, "D:/LFLS/Analyses/C_chilensis_EMN/Correlations/Cor_matrix2.csv")
```

#### Gráfico de correlación para set completo de variables

```r
install.packages("corrplot")
library(corrplot)

plot.new()
corr_plot <- corrplot(cor.matrix, method = "color", type = "lower", 
             mar = c(1,1,1,1), order = "alphabet", tl.col = "black", tl.cex = 0.5, is.corr = FALSE)

# Save output matrix as dataframe (Excel and csv) with rows and columns names

DF <- as.data.frame(cor.matrix)

# Add names to columns and rows

files.1 <- list.files(path = path, pattern = ".asc$", full.names = F)
is.vector(files.1)

colnames(DF) <- files.1
rownames(DF) <- files.1
head(DF)

openxlsx::write.xlsx(DF,"D:/LFLS/Analyses/C_chilensis_EMN/Correlations/Cor_matrix.xlsx", rowNames = TRUE, colNames=T, overwrite = TRUE)
```

#### Feature selection for bioinformaticians using R, 

```r
install.packages("caret")
library(caret) # Classification and Regression Training

DF <- read.table("D:/LFLS/Analyses/C_chilensis_EMN/Correlations/Cor_matrix1.csv", sep = ',')

is.data.frame(DF)
ncol(DF)
nrow(DF)
dim(DF)

colnames(DF)
rownames(DF)

# La matriz DF es la matriz de correlacion anterior que tiene todos los valores de correlacion. The function findCorrelation searches through a "correlation matrix" and returns a vector of integers corresponding to columns to remove to reduce pair-wise correlations. Apply correlation filter at 0.8.

DF.mat <- as.matrix(DF)
is.matrix(DF.mat)
dim(DF.mat)

highlyCor <- findCorrelation(cor(as.matrix(DF.mat)), cutoff = 0.8, exact = TRUE) 
highlyCor  
length(highlyCor)

filtered <- DF.mat[,-highlyCor]
class(filtered)
dim(filtered)

filtered
colnames(filtered)

path = ("D:/LFLS/Analyses/C_chilensis_EMN/Rasters_GEE_asc/M")

files <- gsub("//.asc$","", list.files(path = path, pattern = ".asc$", full.names = F))
files

colnames(DF.mat) <- files
rownames(DF.mat) <- files
head(DF.mat)

mat_keep_rows <- c("dayLST_max.asc","dayLST_mean.asc","DEM.asc","Soil_transpiration.asc")

mat_keep_cols <- c("dayLST_max.asc","dayLST_mean.asc","DEM.asc","Soil_transpiration.asc")

mat_subset <- DF.mat[rownames(DF.mat) %in% mat_keep_rows, colnames(DF.mat) %in% mat_keep_cols]  # Extract rows from matrix
mat_subset  # matrix
mat_subset_DF <- as.data.frame(mat_subset)
is.data.frame(mat_subset_DF)

openxlsx::write.xlsx(mat_subset_DF, "D:/LFLS/Analyses/C_chilensis_EMN/Correlations/Matrix_subset.xlsx", rowNames=T, colNames=T)
```

#### Gráfico de correlación entre variables seleccionadas

```r
corrplot(mat_subset, method = "color", type = "lower", mar = c(1,1,1,1), order = "alphabet", tl.col = "black", tl.cex = 0.6, is.corr = FALSE)

png(height=800, width=800, file="D:/LFLS/Analyses/MNE_garrapatas/Modelado_rostratus/Plots/Cor_plot_final.png", type = "cairo")

ggcorrplot(mat_subset, method = "square", type = "upper", ggtheme = ggplot2::theme_minimal, title = "",
           show.legend = F, show.diag = FALSE,
           colors = c("blue", "white", "red"), outline.color = "gray",
           hc.order = FALSE, hc.method = "complete", lab = T,
           lab_col = "black", lab_size = 7, p.mat = NULL, sig.level = 0.05,
           insig = c("pch", "blank"), pch = 4, pch.col = "black",
           pch.cex = 5, tl.cex = 20, tl.col = "black", tl.srt = 45,
           digits = 2)

dev.off()
```

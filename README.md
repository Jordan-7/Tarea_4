# Tarea_4

En este trabajo se presentara la linea de codigo que muestra los registro de primates en Costa Rica. Se utilizo la base de datos de WorldClim para su elaboración como archivo raster.

## Carga de datos 

library(sf)

library(raster)

library(dplyr)

library(spData)

library(leaflet)

library(plotly)

library(DT)



primates <-
  st_read(
    "https://raw.githubusercontent.com/gf0604-procesamientodatosgeograficos/2021i-datos/main/gbif/primates-cr-registros.csv",
    options = c(
      "X_POSSIBLE_NAMES=decimalLongitude",
      "Y_POSSIBLE_NAMES=decimalLatitude"
    ),
    quiet = TRUE
  )
  
  
  
  
  
  

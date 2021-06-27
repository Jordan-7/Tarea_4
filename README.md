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
  
  
  
cantones <-
  st_read(
    "https://raw.githubusercontent.com/gf0604-procesamientodatosgeograficos/2021i-datos/main/ign/delimitacion-territorial-administrativa/cr_cantones_simp_wgs84.geojson",
  quiet = TRUE
    )
  
  
st_crs(primates) = 4326
  
  
  
provincias <-
  st_read(
    "https://raw.githubusercontent.com/gf0604-procesamientodatosgeograficos/2021i-datos/main/ign/delimitacion-territorial-administrativa/cr_provincias_simp_wgs84.geojson",
    quiet = TRUE
  )
  
  
 st_crs(cantones) = 4326
  
 
 
## Datos de especies filtradas


primates <-
  primates %>%
  st_join(cantones["canton"])
  
  
  
primates <-
  primates %>%
  st_join(provincias["provincia"])
  
  
  
alou <- primates %>%
  select(species,
         provincia,
         canton,
         eventDate,
         decimalLongitude,
         decimalLatitude) %>%
  filter(species == "Alouatta palliata")
  
  
  
saimi <- primates %>%
  select(species,
         provincia,
         canton,
         eventDate,
         decimalLongitude,
         decimalLatitude) %>%
  filter(species == "Saimiri oerstedii")
  
  
  
ate <- primates %>%
  select(species,
         provincia,
         canton,
         eventDate,
         decimalLongitude,
         decimalLatitude) %>%
  filter(species == "Ateles geoffroyi")
  
  
  
cebu <- primates %>%
  select(species,
         provincia,
         canton,
         eventDate,
         decimalLongitude,
         decimalLatitude) %>%
  filter(species == "Cebus capucinus")
  
  
  
  ## Datos raster
 
 
alt <- getData(
  "worldclim",
  var = "alt",
  res = .5,
  lon = -84,
  lat = 10
)



altitud <-
  alt %>%
  crop(provincias) %>%
  mask(provincias)
  
  
  
  

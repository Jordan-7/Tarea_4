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
  
  
  ## Tabla de las especies de primates
  
 
  Se muestra una tabla de todos los registros de las 4 especies de primates.
  
  
  primates %>%
  st_drop_geometry() %>%
  select(family, species, stateProvince, canton, eventDate) %>%
  datatable(
    colnames = c("Familia", "Especie", "Provincia", "Canton", "Fecha"),
    options = list(
      searchHighlight = TRUE,
      language = list(url = '//cdn.datatables.net/plug-ins/1.10.11/i18n/Spanish.json')
    )
  )
  
  
  ## Grafico I Porcentaje de poblacion de especies de primates
  
  
 ### Conteo de especies
  
sum(with(primates,species == "Alouatta palliata"))
sum(with(primates,species == "Saimiri oerstedii"))
sum(with(primates,species == "Ateles geoffroyi"))
sum(with(primates,species == "Cebus capucinus"))


## Grafico pastel

nespecies <- c(1994, 453, 599, 1463)

etiqueta <- c("Alouatta palliata",
              "Saimiri oerstedii",
              "Ateles geoffroyi",
              "Cebus capucinus")

colors <- c('006633', '33CC33',
            '66CCCC', '006699',
            '330099')

t <- list(family = "Bookman",
          size = 11,
          color = '333333')

primates %>%
  plot_ly(
    labels = ~ etiqueta,
    values = ~ nespecies,
    type = "pie",
    marker = list(colors = colors,
                  line = list(color = '#000000', width = 1)))%>%
  config(locale = "es")%>%
  layout(
    title = "Abundancia de primates en Costa Rica",
    font = t,
    xaxis = list(
      showgrid = FALSE,
      zeroline = FALSE,
      showticklabels = FALSE
    ),
    yaxis = list(
      showgrid = FALSE,
      zeroline = FALSE,
      showticklabels = FALSE
    )
  )


## Mapa de Costa Rica de especies especies de primates reportadas en el pais

### Bloque popups 

palou <- paste0("<b>", "Especie: ","</b>",
                  (alou$species),"<br>",
                  "<b>","Provincia: ","</b>",
                  (alou$provincia),"<br>",
                  "<b>", "Canton: ","</b>",
                  (alou$canton),"<br>",
                  "<b>", "Fecha: ","</b>",
                  (alou$eventDate),"<br>",
                  "<b>", "Coordenadas: ","</b>",
                  (alou$decimalLongitude),
                  (alou$decimalLatitude)
)
                  
psaimi <- paste0("<b>", "Especie: ","</b>",
                  (saimi$species),"<br>",
                  "<b>","Provincia: ","</b>",
                  (saimi$provincia),"<br>",
                  "<b>", "Canton: ","</b>",
                  (saimi$canton),"<br>",
                  "<b>", "Fecha: ","</b>",
                  (saimi$eventDate),"<br>",
                  "<b>", "Coordenadas: ","</b>",
                  (saimi$decimalLongitude),
                  (saimi$decimalLatitude)
)


pate <- paste0("<b>", "Especie: ","</b>",
                   (ate$species),"<br>",
                   "<b>","Provincia: ","</b>",
                   (ate$provincia),"<br>",
                   "<b>", "Canton: ","</b>",
                   (ate$canton),"<br>",
                   "<b>", "Fecha: ","</b>",
                   (ate$eventDate),"<br>",
                   "<b>", "Coordenadas: ","</b>",
                   (ate$decimalLongitude),
                   (ate$decimalLatitude)
  )

pcebu <- paste0("<b>", "Especie: ","</b>",
                   (cebu$species),"<br>",
                   "<b>","Provincia: ","</b>",
                   (cebu$provincia),"<br>",
                   "<b>", "Canton: ","</b>",
                   (cebu$canton),"<br>",
                   "<b>", "Fecha: ","</b>",
                   (cebu$eventDate),"<br>",
                   "<b>", "Coordenadas: ","</b>",
                   (cebu$decimalLongitude),
                   (cebu$decimalLatitude)
  )
  
  ### Color raster
  
  rcol <- colorNumeric(c("#00CC00", "#FFFF66", "#FF0000"),
                       values(altitud),
                       na.color = "transparent")


 ### Mapa
  
  primates %>%
    leaflet() %>%
    addProviderTiles(providers$OpenStreetMap.Mapnik, 
                     group = "OpenStreetMap") %>%
                     
    addProviderTiles(providers$Stamen.TonerLite, 
                     group = "Stamen Toner Lite") %>%
                     
    addProviderTiles(providers$Esri.WorldImagery, 
                     group = "Imágenes de ESRI") %>%
                     
    addRasterImage(
      altitud, 
      colors = rcol, 
      opacity = 0.8,
      group = "Altitud") %>%
      
    addCircleMarkers(
      data = alou,
      stroke = F,
      radius = 4,
      fillColor = '#006666',
      fillOpacity = 1,
      popup = palou,
      group = ("Alouatta palliata")
    ) %>%
    
    addCircleMarkers(
      data = saimi,
      stroke = F,
      radius = 4,
      fillColor = '#660033',
      fillOpacity = 1,
      popup = psaimi,
      group = "Saimiri oerstedii"
    ) %>%
    
    addCircleMarkers(
      data = ate,
      stroke = F,
      radius = 4,
      fillColor = '#003300',
      fillOpacity = 1,
      popup = pate,
      group = "Ateles geoffroyi"
    ) %>%
    
    addCircleMarkers(
      data = cebu,
      stroke = F,
      radius = 4,
      fillColor = '#666600',
      fillOpacity = 1,
      popup = pcebu,
      group = "Cebus capucinus"
    ) %>%
    
    addLayersControl(
      baseGroups = c("OpenStreetMap", "Stamen Toner Lite", 
                     "Imágenes de ESRI"),
      overlayGroups = c("Alouatta palliata", "Saimiri oerstedii", 
                        "Ateles geoffroyi", "Cebus capucinus"
                        ,"Altitud")
    ) %>%
    
    addMiniMap(tiles = providers$Stamen.OpenStreetMap.Mapnik,
               position = "bottomleft",
               toggleDisplay = TRUE
    )







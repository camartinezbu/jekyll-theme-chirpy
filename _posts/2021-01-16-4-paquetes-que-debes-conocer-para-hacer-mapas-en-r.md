---
title: 4 paquetes que debes conocer para hacer mapas en R
date: 2021-01-17 09:45:00 -0500
categories: [R, Tutorial]
tags: [mapas, r, leaflet, sf, ggplot, tmap]
image: /assets/img/posts/2021-01-17-4-paquetes-que-debes-conocer-para-hacer-mapas-en-r/pexels-andrew-neel-2859169.jpg
excerpt: Aqui una alternativa a ArcGIS o QGIS.
---

Así que decidiste aprender a usar R para trabajar con mapas. Esta guia te dará las herramientas básicas para que puedas hacerlo, incluyas visualizaciones de mapas en tu flujo de trabajo y te motives a hacer análisis espacial con este lenguaje.

## Código vs interfaces de usuario

Antes de comenzar, lo primero que tienes que saber es que para hacer mapas en R vas a tener que escribir código. No es código muy complejo si tienes una familiaridad básica con este lenguaje de programación, pero quizás al principio tiene una curva de aprendizaje más empinada que programas como ArcGIS (o su alternativa Open Source [QGIS](https://qgis.org/en/site/forusers/download.html)) en los que puedes interactuar a través de menús y botones con tu mouse.

Sin embargo, si ya estás trabajando en un ambiente de R, si quieres automatizar la creación de muchos mapas con estructuras de control - los bucles con `for`-, o si te gustaría incluir fácilmente tu análisis en un dashboard de `shiny`, realizar mapas de esta forma puede ser una muy buena alternativa.

## ¿Dónde consigo los mapas?

Lo primero que te puedes estar preguntando es: *¿Pero y dónde encuentro los mapas para trabajar?* Afortunadamente, en línea hay muchos repositorios de información con atributos espaciales. Aqui va una lista de enlaces que te pueden ser de utilidad para empezar.

### Para Colombia

- [El Marco Geoestadístico Nacional](https://geoportal.dane.gov.co/servicios/descarga-y-metadatos/descarga-mgn-marco-geoestadistico-nacional/) del DANE contiene los polígonos del país, sus departamentos y municipios, con revisiones en 2005, 2012, 2017, 2018 y 2020.
- [El portal de Datos Abiertos del IGAC](https://geoportal.igac.gov.co/contenido/datos-abiertos-igac) tiene información de agrología, cartografía, geodesia, entre otros.
- [El IDECA](https://www.ideca.gov.co/buscador?topic=All&metadata=All&newest=All&entity=All&resource=All&content_type=map&res=true&sort_by=created&sort_order=DESC) tiene una gran cantidad de información para Bogotá para un gran número de temáticas: Ordenamiento Territorial, Salud, Ambiente, Seguridad, entre otros.
- [GeoMedellín](https://www.medellin.gov.co/geomedellin/index.hyg) aloja numerosos conjuntos de información geolocalizada para Medellín, también separados por temas.

### Para el resto del Mundo

- El [GADM](https://gadm.org/download_country_v3.html) del Centro de Ciencias Espaciales de la Universidad de California, Davis tiene los shapefiles para todos los países y sus subdivisiones. Dependiendo el país que escojas habrá mayor o menor calidad de la información.

Esta no es una lista exhaustiva de fuentes de información, así que si conoces otra fuente que creas que debe estar allí, compártemela para añadirla.

## El formato shapefile

El formato más común con el que te vas a encontrar es el *shapefile*. Este formato es desarollado por ESRI, la compañía que creó ArcGIS, y consiste en varios archivos con extensiones `*.dbf`, `*.prj`, `*.sbn`, `*.sbx`, `*.shp`, `*.shx`, entre otros. 

Para efectos prácticos, siempre que querramos importar o modificar el shapefile en R, vamos a referirnos únicamente al archivo `*.shp` y R se encargará del resto. Asímismo, es importante que **no borres los archivos con las demás extensiones**, dado que para que el shapefile funcione los necesitas a todos.

Eventualmente, te puedes encontrar con otros formatos como el *GeoPackage* o *geodatabase*, pero la manera de trabajar con ellos en R es my similar a la del *shapefile*.

## 1. Simple Features: sf

Simple Features es un estándar para la representación de objetos espaciales de la vida real a través de geometrías -puntos, lineas, polígonos, etc. - en los sistemas de información geográfica. El paquete `sf` implementa este estándar en R y es con el que vamos a trabajar.

En esta guía no vamos a cubrir cosas específicas como las funciones para personalizar los mapas, el sistema de coordenadas de los shapefiles u operaciones con y entre shapes. Por ahora solo vamos a importar un shape a R.

Voy a usar el mapa de las localidades de la Bogotá [disponible en la página del IDECA](https://www.ideca.gov.co/recursos/mapas/localidad-bogota-dc) para hacer los siguiente ejemplos. Lo primero, y lo único, que vamos a hacer con `sf` será importar el shapefile. Para ello, vamos a correr el siguiente código:

```r
install.packages('sf') # Si no has instalado el paquete aún

library(sf)

mi_shape <- sf::st_read('Loca.shp') # Debes modificar el contenido de st_read dependiendo de dónde tengas guardado el archivo.
```

Si todo salio bien, deberías tener un objeto en r con clase `sf`, que contiene un mapa con las localidades de Bogotá.

## 2. ggplot2

La primera forma de visualizar este objeto es usando el paquete `ggplot2` del `tidyverse`. Este paquete es una de las librerías más usadas para crear gráficos en R y tiene muchas opciones para ajustar cada detalle.

Visualizar un mapa en ggplot es muy sencillo. Así como tienes funciones como `geom_point()` o `geom_bar()` para crear un gráfico de puntos o un gráfico de barras, el paquete `sf` contiene una función `geom_sf()` que se enlaza con `ggplot2` y te permite visualizar el mapa.

A modo de ejemplo, el resultado del siguiente código será:

```r
library(ggplot2)

ggplot(data = mi_shape) +
    geom_sf()
```

![Mapa de las localidades de Bogotá usando ggplot2](/assets/img/posts/2020-01-17-4-paquetes-que-debes-conocer-para-hacer-mapas-en-r/mapa_ggplot.png)
*Mapa de las localidades de bogotá usando ggplot2*

De aqui en adelante, puedes jugar con las opciones de temas de ggplot2, para cambiar el color de fondo, la fuente, los ejes, entre muchas otras cosas. Por ejemplo, intenta añadir al final del código anterior `+ theme_void()`. Casi cualquier configuración que funcionaría para un ggplot normal, funciona para un mapa hecho de esta forma.

## 3. tmap

`tmap` es un paquete diseñado específicamente para la creación de mapas temáticos con `sf`. `tmap` tiene una sintaxis muy parecida a la de ggplot y tiene la ventaja de que no tienes que lidiar con etiquetas de los ejes, color de fondo, etc. Ya está diseñado para sacar un mapa más limpio.

Al ejecutar los siguientes comandos, tendrás como resultado:

```r
library(tmap)

tm_shape(mi_shape) +
    tm_polygons()
```

![Mapa de las localidades de Bogotá usando tmap](/assets/img/posts/2020-01-17-4-paquetes-que-debes-conocer-para-hacer-mapas-en-r/mapa_tmap.png)
*Mapa de las localidades de bogotá usando tmap*

Una vez estés familiarizado con este paquete, te recomiendo revisar el paquete `tmaptools`, que contiene algunas funciones útiles para trabajar con `tmap`.

## 4. leaflet

Finalmente, el paquete `leaflet` ofrece una funcionalidad mucho más interesante: Interactividad. Con este paquete, puedes crear un mapa en el que podrás arrastrar, hacer zoom, definir que hace cuando haces clic o cuando pasas el mouse por encima de un polígono, entre muchas otras cosas.

Lo mejor de todo, la sintaxis es parecida a los dos métodos anteriores, por lo que construir un mapa más complejo sigue siendo muy sencillo usando `leaflet`.

Del siguiente código, resulta:

```r
library(leaflet)

leaflet(data = mi_shape) %>%
    addTiles() %>% # Esta función añade el mapa de fondo, que peudes cambiar por muchas opciones
    addPolygons(weight = 0.5, label = ~LocNombre)
```

<div style = "height: 30em; padding: 0; display: flex; justify-content: center;" class = "htmlwidget" >
    <iframe src="../../assets/img/posts/2020-01-17-4-paquetes-que-debes-conocer-para-hacer-mapas-en-r/mapa_leaflet.html" style = "width: 100%; height:100%;"></iframe>
</div>
*Mapa de las localidades con leaflet*

Una gran ventaja de trabajar con `leaflet` es que puedes integrar los mapas interactivos con tableros de visualización con `shiny`, conservando la interactividad con los usuarios. Si quieres saber cómo funciona `shiny`, revisa la introducción que hice en [esta entrada de blog](https://camartinezbu.github.io/posts/como-hacer-un-dashboard-en-r/).

## Bonus: Haz el mapa de las calles de tu ciudad

Recientemente encontré [este tutorial](https://ggplot2tutor.com/streetmaps/streetmaps/) de Christian Burkhart ([@ChBurkhart](https://twitter.com/ChBurkhart)) en el que describe cómo hacer un mapa usando `osmdata` y `ggplot2`. Te invito a que hagas este ejercicio, que no te tomará más de 5 minutos. [Yo lo hice para Bogotá](https://twitter.com/camartinezbu/status/1340697885042745347) y no quedó tan mal.

Con estos 4 paquetes, tienes lista una caja de herramientas para empezar a trabajar con mapas y al jugar con las opciones, podrás hacer visualizaciones profesionales y de manera muy fácil.

## Lecturas relacionadas

- [Documentación de sf](https://r-spatial.github.io/sf/index.html)
- [Documentación de tmap](https://cran.r-project.org/web/packages/tmap/vignettes/tmap-getstarted.html)
- [Documentación de leaflet para R](https://rstudio.github.io/leaflet/)
---
title: Hablemos de proyecciones
date: 2021-01-31 10:00:00 -0500
categories: [Mapas, Tutorial]
tags: [mapas, r, leaflet, sf, ggplot, tmap]
image: /assets/img/posts/2021-01-31-hablemos-de-proyecciones/hero.jpg
excerpt: ¿Te has preguntado por qué los mapas son como son?
---

Ahora que conoces [algunos paquetes para visualizar mapas](https://camartinezbu.github.io/posts/4-paquetes-que-debes-conocer-para-hacer-mapas-en-r/), es importante que entiendas qué son las proyecciones, pues te van a servir para hacer análisis espacial más adelante.

## Un problema de geometría

Puede parecer obvio lo que voy a decir, pero la tierra es un objeto tridimensional que podemos aproximar matemáticamente a través de un [geoide](https://es.wikipedia.org/wiki/Geoide). Las ubicaciones en la superficie de este geoide se suelen describir con latidudes y longitudes, que representan las direcciones Norte/Sur y Este/Oeste, tomando como referencia la línea del Ecuador y el Meridiano de Greenwich.

Ahora bien, no siempre tenemos a nuestra disposición un modelo tridimensional del globo para acercarnos lo más posible a la ubicación de un elemento en su superficie. En general usamos mapas para este propósito, pero eso introduce una dificutad y es que tenemos que transformar las ubicaciones de esta superficie a un plano. Este no es un asunto menor.

En el *Theorema Egregium*, el matemático Carl Friederich Gauss probó que la superficie de una esfera no se puede representar en un plano sin distorsión. En la práctica, esto quiere decir que cuando hacemos un mapa en un plano, no necesariamente se van a conservar todas sus propiedades como el área, la forma, la dirección, la distancia, entre otras.

## Las proyecciones

No hay una única forma de transformar las ubicaciones en la superficie de la tierra en un plano. De hecho, hay una gran cantidad de proyecciones distintas que buscan preservar alguno de los atributos descritos con anterioridad y que, según el internet, dicen [cosas sobre nuestra personalidad](https://xkcd.com/977/).

En vez de pegar imágenes de las proyecciones, que puedes encontrar fácilmente en linea, para este post quiero dibujar algunas proyecciones en R usando el paquete `sf`. Primero veamos las proyecciones y luego te enseñaré cómo hacer estos mapas.

La lista de proyecciones que te voy a mostrar no es para nada exhaustiva y así como existen sistemas de coordenadas para el mundo, los hay para paises e incluso ciudades.

### Mercator

La proyección lleva el nombre del cartógrafo Gerardus Mercator y probablemente sea esta la que te imagines cuando pienses en un mapa del mundo. En la proyección Mercator se mantienen la dirección y la forma pero se distorsiona el tamaño a medida que se aleja del Ecuador.

![Proyección de Mercator hecha en R](/assets/img/posts/2021-01-31-hablemos-de-proyecciones/mapa_mercator.png)
*Proyección Mercator hecha en R*

### Mollweide

Esta proyección se caracteriza por conservar el área en cualquier lugar del mapa. Fue creada por el matemático Karl Brandan Mollweide en 1805. Algunas de las aplicaciones de esta proyección son los mapas de la Radiación de fondo de microondas o de concentración de químicos en el mar.

![Proyección de Mollweide hecha en R](/assets/img/posts/2021-01-31-hablemos-de-proyecciones/mapa_mollweide.png)
*Proyección Mollweide hecha en R*

### Plate Carrée

Esta es una proyección equirrectangular, es decir, en la que los meridianos y paralelos se ubican como líneas  con igual distancia entre si. Esto implica que no se conserva ni el área ni la forma, pero a pesar de esto, se suele utilizar para mapas temáticos.

![Proyección Plate Carrée hecha en R](/assets/img/posts/2021-01-31-hablemos-de-proyecciones/mapa_plate_carree.png)
*Proyección Plate Carrée hecha en R*

### Aitoff

La proyeccción de Aitoff conserva la dirección y la distancia de todos los puntos al centro del mapa, donde se cruza el meridiano de Greenwich y el Ecuador.

![Proyección Aitoff hecha en R](/assets/img/posts/2021-01-31-hablemos-de-proyecciones/mapa_aitoff.png)
*Proyección Aitoff hecha en R*

### Winkel tripel

Esta proyección fue propuesta por el cartógrafo Oswald Winkel y tiene la particularidad de ser la media aritmética entre la proyección equirrectangular y la proyección de Aitoff.

La proyección de Winkle busca minimizar tres tipos de distorsión (de ahí el *tripel* en su nombre): área, dirección y distancia, y es usada por la National Geographic Society.

![Proyección Winkel tripel hecha en R](/assets/img/posts/2021-01-31-hablemos-de-proyecciones/mapa_winkel.png)
*Proyección Winkel tripel hecha en R*

## Flujo de trabajo en R

Para los mapas anteriores usé un shapefile descargado del repositorio de información geográfica [geoBoundaries](https://www.geoboundaries.org/index.html#tabs1-js), en el que puedes encontrar la información de la división administrativa de muchos países en varias resoluciones. En este caso, yo descargué la información del mundo sólo a nivel de paises, con baja resolución.

### Importando el mapa

Como siempre, vamos a importar el shapefile usando la función `st_read()`.

```r
library(sf)
library(dplyr)
library(rmapshaper)

mapa_base <- sf::st_read("geoBoundariesCGAZ_ADM0.shp") %>%
rmapshaper::ms_simplify(sys = T, keep = 0.01) %>%
sf::st_as_sf()
```

En el bloque de código anterior hago dos cosas adicionales. En primer lugar, del paquete `dplyr` uso el operador *pipe* para encadenar las salidas de cada linea de código con la entrada de la siguiente. El operador *pipe* es la secuencia de caracteres `%>%`, y si has trabajado con el `tidyverse` te será familiar.

En segundo lugar, uso la función `ms_simplify` del paquete `rmapshaper` para simplificar las geometrías y disminuir el tiempo que se demora en dibujar cada mapa. Dado que `rmapshaper` aún no tiene soporte completo para el paquete `sf`, internamente la función lo convierte en un objeto distinto, por lo que al final uso la función `st_as_sf()` para que el mapa vuelva a funcionar con `sf`.

### Funciones para trabajar con proyecciones

Para estos efectos, hay dos funciones clave. La primera es la función `st_crs()`, que permite extraer cuál es el sistema de coordenadas, y por extensión la proyección, del objeto con el que estamos trabajando. 

En general, los sistema de coordenadas que vas a encontrar siguen la nomencluatura "EPSG:XXXX". La primera parte es por el European Petroleum Survey Group, o EPSG. Esta es una organización que mantiene una base de datos de sistemas de coordenadas geográficas. La segunda parte es el código en dicha base de datos con el que podrás acceder al sistema de coordenadas que necesites.

Si corremos la siguiente línea de código, 

```r
sf::st_crs(mapa_base)
```

R nos debe devolver que este objeto tiene un ID EPSG de 4326. Puedes consultar más información sobre este y otros sistemas de coordenadas en [este link](https://epsg.io/4326).

La otra función que nos va a ayudar es `st_transform()`. Con esta función, podremos convertir un mapa de un sistema de coordenadas geográficas a otro. Por ejemplo, si quiero convertir el mapa base al sistema EPSG:3116, que es el sistema oficial del Instituto Agustin Codazzi para Colombia, tendría que hacer lo siguiente:

```r
mapa_nuevo <- sf::st_transform(mapa_base, crs = st_crs("EPSG:3116"))
```

> Nota: esta transformación en particular genera un error al intentar dibujar el mapa. Lo importante es que sepas cómo usar la función `st_transform` en caso que lo necesites.

### Código para dibujar los mapas

Con las dos funciones anteriores, ya tenemos los elementos necesarios para transformar las proyecciones. Acá les dejo el código que usé para dibujar los mapas que aparecen más arriba en el post.

```r
library(dplyr)
library(tmap)
library(tmaptools)
library(sf)

mapa_base <- sf::st_read('~/Downloads/World Map/geoBoundariesCGAZ_ADM0.shp') %>%
  rmapshaper::ms_simplify(sys=T, keep = 0.01) %>%
  st_as_sf()

# Proyección de Mercator
mapa_mercator <- sf::st_transform(mapa_base, crs = st_crs("EPSG:3395"))

tm_shape(mapa_mercator, bbox = tmaptools::bb(mapa_mercator, 
                                             ylim=c(-15496570.74, 18764656.23),
                                             xlim=c(-20026376.39, 20026376.39))) +
  tm_polygons() +
  tm_graticules()

# Proyección de Molweide
mapa_mollweide <- sf::st_transform(mapa_base, crs = st_crs("ESRI:54009"))

tm_shape(mapa_mollweide) +
  tm_polygons() +
  tm_graticules()

# Proyección Plate Carrée
mapa_plate_carree<- sf::st_transform(mapa_base, crs = st_crs("EPSG:32662"))

tm_shape(mapa_plate_carree) +
  tm_polygons() +
  tm_graticules()

# Proyección Aitoff
mapa_aitoff <- sf::st_transform(mapa_base, crs = st_crs("ESRI:54043"))

tm_shape(mapa_aitoff) +
  tm_polygons() +
  tm_graticules()

# Proyección de Winkel tripel
mapa_winkel <- sf::st_transform(mapa_base, crs = st_crs("ESRI:54019"))

tm_shape(mapa_winkel) +
  tm_polygons() +
  tm_graticules()
```
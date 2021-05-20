---
title: Mapas de calor como calendarios en R
date: 2021-05-19 11:30:00 -0500
categories: [R, Tutorial]
tags: [r, calendar, heatmap, dataviz]
image: /assets/img/posts/2021-05-19-mapas-de-calor-como-calendarios-en-R/hero.jpg
excerpt: Una manera creativa de presentar datos con fechas.
---

Después de una pausa de algunas semanas vuelvo a escribir posts semanales en el blog. Para comenzar, quiero compartirles una visualización que me llamó mucho la atención al leer el libro [Better Data Visualizations](https://www.amazon.com/Better-Data-Visualizations-Scholars-Researchers/dp/0231193114) de Jonathan Schwabish: Los *Heatmap calendars*, que acá voy a traducir -probablemente mal- como mapas de calor en forma de calendarios. Buena parte del insumo para este post viene de [este tutorial](https://vietle.info/post/calendarheatmap/) de Viet Le.

## Paquetes necesarios

Para este ejercicio voy a usar los siguientes paquetes: `readxl` para importar la base de datos que se encuentran en un archivo .xls, el `tidyverse` para la limpieza de los datos y la visualización con `ggplot2`, y `lubridate` para un par de operaciones puntuales con las fechas.

Adicionalmente, usaré `shadowtext` para mejorar un poco la legibilidad del texto en el calendario al añadir sombras. Sin embargo, esto no es fundamental para hacer el calendar heatmap.

## Los datos

Voy a usar un archivo de excel que contiene los registros de violencia intrafamiliar en Colombia durante 2019, disponibles para descarga en la página de [Estadística Delictiva de la Policía Nacional](https://www.policia.gov.co/grupo-informaci%C3%B3n-criminalidad/estadistica-delictiva).

Esta base de datos contiene los siguientes campos de información: Departamento, Municipio, Código DIVIPOLA del municipio, Arma empleada, Fecha del registro, Sexo de la víctima, Grupo de edad de la víctima y un contador de cantidad de hechos. Para este ejercicio sólo nos interesa usar la fecha.

## Importación

Lo primero que hay que hacer es cargar los paquetes que vamos a usar. Posteriormente, cargo la base de datos llamando de la función `read_xlsx()` del paquete `readxl` y la guardo en un objeto llamado `vif`.

Adicionalmente, y por buenas prácticas, voy a definir la zona horaria de las fechas registradas en Bogotá con la función `tz()`.

```r
library(tidyverse)
library(readxl)
library(lubridate)

vif <- read_xlsx("~/Downloads/violencia_intrafamiliar_2019_0.xlsx", 
                 range = cell_limits(c(11, 1), c(113894, 8)))

tz(vif$`FECHA HECHO`) <- "America/Bogota"
```

Acá vale la pena mencionar que por alguna razón escribir el rango de las celdas en el argumento `range` de `read_xslx()` en la forma `A1:H113894` me generaba un error en la lectura. Una búsqueda rápida en internet me llevó a escribir el rango dentro de la función `cell_limits()`, lo que corrigió el problema.

## Procesamiento

Aquí viene la parte mas importante. En esencia, lo que hacemos para hacer el mapa de calor en forma de calendario es colorear doce grillas de 7x4 -7 días y 4 semanas-, donde el color de cada celda obedece a un conteo de registros de violencia intrafamiliar.

Sin embargo, como el resultado final debe parecer un calendario, necesitamos definir para cada registro en qué semana del mes y en qué día de la semana ocurrió. De lo contrario todos los meses empezarían el mismo día. Lunes o domingo, dependiendo del formato de calendario que se quiera usar.

Para ello, vamos a ejecutar el siguiente bloque de código:

```r
historico_diario <- vif %>%
  transmute(FECHA = ymd(`FECHA HECHO`), CANTIDAD) %>%
  group_by(FECHA) %>%
  summarise(REGISTROS = sum(CANTIDAD)) %>%
  ungroup() %>%
  mutate(DIA = wday(FECHA, label = T, week_start = 1, locale = "es_ES")) %>%
  mutate(MES = month(FECHA, label = T, locale = "es_ES")) %>%
  mutate(SEMANA = isoweek(FECHA))

historico_diario$SEMANA[historico_diario$MES=="dic" & historico_diario$SEMANA == 1] = 53 

historico_diario <- historico_diario %>%
  group_by(MES) %>%
  mutate(SEMANA_MES = 1 + SEMANA - min(SEMANA))

```

Vamos por partes. En el primer bloque de código creo una variable `FECHA` que contiene la fecha de la base de datos original en un formato que puede trabajar `lubridate` y selecciono la columna `CANTIDAD`. Luego hago una suma de los registros para cada uno de los días del año y calculo 3 variables nuevas:

- `DIA`: Una variable  que contiene qué día de la semana (lunes, martes, miercoles, ...) es cada fecha. Adicionalente, defino que el idioma para mostrar las etiquetas de los días sea español, usando el argumento `locale`.
- `MES`: Una variable  que indica el mes del registro, con las etiquetas en español.
- `SEMANA`: Una variable que indica la semana del año a la que pertenece cada fecha,de 1 a 52, usando la función `isoweek()`.

Ahora bien, como puedes ver, hay una línea de código que modifica manualmente la variable semana. Esto sucede porque `isoweek()` define automáticamente la última semana del año como 1, si contiene algún día del año siguiente. Por esta razón, definimos que si el mes es diciembre y la semana del año es 1, la convierta en la semana 53.

Finalmente, se crea una variable `SEMANA_MES`, de tal manera que se tenga a cuál semana de cada mes pertenece la fecha (1-4) y no la semana del año como en la variable `SEMANA` (1-52).

## El calendario

Ahora si, después de todo lo anterior, podemos generar el calendario con el siguiente código:

```r
historico_diario %>%
  ggplot(aes(x = DIA, y = -SEMANA_MES, fill = REGISTROS)) +
  geom_tile(colour = "white") +
  geom_text(aes(label = day(FECHA)), size = 2.5, color = "white") +
  theme(aspect.ratio = 1/2,
        legend.position = "right",
        axis.title.x = element_blank(),
        axis.title.y = element_blank(),
        axis.text.y = element_blank(),
        panel.grid = element_blank(),
        axis.ticks = element_blank(),
        panel.background = element_blank(),
        legend.title.align = 0.5,
        strip.background = element_blank(),
        strip.text = element_text(face = "bold", size = 15),
        panel.border = element_rect(colour = "grey", fill=NA, size=1),
        plot.title = element_text(hjust = 0.5, size = 18, face = "bold",
                                  margin = margin(0,0,0.5,0, unit = "cm"))) +
  scale_fill_viridis_c(name = "Registros", direction = -1, 
                       guide = guide_colourbar(title.position = "top", 
                                               direction = "vertical")) +
  facet_wrap(~MES, nrow = 4, ncol = 3, scales = "free") +
  labs(title = "Comportamiento de la violencia intrafamiliar \nen Colombia durante 2019",
       caption = "Fuente: SIEDCO")
```

Si bien esto puede parecer muy complejo, la mayoría de instrucciones tienen que ver con que no salgan colores ni lineas en el fondo del gráfico, como es el comportamiento por defecto de `ggplot2`, y algunos ajustes del tamaño y posición del texto.

Lo más importante está en que entiedas el llamado inicial de `ggplot()`, en el que se usan las variables de `DIA` y `SEMANA_MES` para construir la grilla que mencioné anteriormente, y se mapea el color a la variable de `REGISTROS`.

Cuando corras ese código, vas a tener el siguiente resultado:

![Calendar Heatmap](/assets/img/posts/2021-05-19-mapas-de-calor-como-calendarios-en-R/calendar_heatmap.png)
*Mapa de calor en forma de calendario*

## Un ajuste menor al texto

Como te podrás dar cuenta, los números en color blanco no son fáciles de leer cuando el color del día es más claro. Esto podrías solucionarlo cambiando el color del texto o, como hice yo, usando el paquete `shadowtext`. 

Este paquete incluye una función para `ggplot2` que funciona muy parecido a `geom_text()`, sólo que añade una sombra alrededor del texto: `geom_shadowtext()`. Reemplazando esta función en el código de arriba, ¡queda listo el mapa de calor con forma de calendario!

![Calendar Heatmap Shadow](/assets/img/posts/2021-05-19-mapas-de-calor-como-calendarios-en-R/calendar_heatmap_shadow.png)
*Mapa de calor en forma de calendario con sombras*

Con esta visualización podemos ver rápidamente que parece haber unos picos de registros de violencia intrafamiliar en fechas como navidad, año nuevo y el día de la madre, y que en términos generales parece que se registran más casos en los fines de semana. ¿Ves lo fácil que es extraer toda esta información con una sóla mirada?

Espero que te haya gustado este post y que ahora puedas experimentar con mapas de calor en forma de calendario para datos de otros temas.

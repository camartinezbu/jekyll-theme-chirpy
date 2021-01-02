---
title: ¿Cómo hacer un dashboard en R?
date: 2020-08-17 12:10:00 -0500
categories: [R, Tutorial]
tags: [tablero, dashboard, shiny, covid, dataviz, r]
image: /assets/img/posts/2020-08-17-como-hacer-un-dashboard-en-r/pexels-energepiccom-159888.jpg
excerpt: Tableau y Power BI no son las únicas formas de hacer un tablero interactivo.
---

Los tableros o *dashboards* son herramientas muy útiles para extraer conclusiones de los datos, en ocasiones más convenientes que realizar muchas gráficas en excel para luego incluirlas en una presentación de diapositivas. En este artículo se explicará cómo hacer un tablero básico de visualización en R y se compartirá el enlace para un ejemplo con base en la información de covid-19 del Instituto Nacional de Salud de Colombia.

## Los tableros de visualización

Seguramente has escuchado del furor alrededor de Tableau o PoweBI. Al revisar la documentación de sus servicios, la promesa de valor que ofrecen estas compañías está en ["aprovechar el poder de los datos"](https://www.tableau.com/es-es) o ["eliminar la brecha entre los datos y la toma de decisiones"](https://powerbi.microsoft.com/es-es/what-is-power-bi/).

Los tableros de visualización son formas eficientes de ver, y en algunos casos interactuar, con información relevante para un problema determinado. Piensa en la evolución de un reporte escrito que se realiza para el jefe de un proyecto, en la que la información más relevante se presenta en un sólo vistazo, es interactiva y, dependiendo de los datos disponibles, en tiempo real.

Si bien las plataformas mencionadas son muy populares y robustas, no necesariamente son la mejor alternativa en todos los casos. Si quieres hacer una exploración de tus datos de manera rápida para hacer limpieza o preprocesamiento, o si quieres aprovechar alguna característica especial de una librería de tu lenguaje de programación preferido, existen alternativas que pueden hacer esta tarea más fácil y sin recurrir a servicios de terceros.

## Shiny y shinydashboard

Para el caso de R, `shiny` es la librería por excelencia para realizar tableros de visualización con muy pocas líneas de código, desarrollada por RStudio. Por su parte, `shinydashboard` es un paquete complementario que ofrece un diseño con un encabezado, una barra lateral y un cuerpo para el tablero.

La estructura más básica de un tablero de visualización hecho con shiny tiene la forma:

```r
library(shiny)
library(shinydashboard)

ui <- dashboardPage(
    dashboardHeader(),
    dashboardSidebar(),
    dashboardBody()
)

server <- function(input, output) {}
```

Como se puede ver, hay dos partes principales. La interfaz de usuario o `ui` es el objeto que define cómo será la visualización del tablero y los elementos que estarán allí incluidos. Por su parte, el servidor o `server` contiene todos los cálculos y operaciones necesarios para generar los elementos que se van a visualizar.

Hay dos formas de escribir un tablero en scripts de R. La primera es generar un script que se llame `app.R` e incluir la interfaz y el servidor tal cual como aparecen en el ejemplo. La otra es definirlos en scripts separados y llamarlos `ui.R` y `server.R`. En mi opinión, la segunda forma es la más cómoda porque no es necesario desplazarse hacia arriba y hacia abajo cada vez que se añade algún elemento al tablero.

Para lo que sigue de este artículo, el encabezado del tablero tendrá únicamente el título del tablero, la barra lateral estará deshabilitada y el cuerpo estará compuesto por únicamente una fila. Para mayor información sobre esta organización, la [documentación](https://rstudio.github.io/shinydashboard/structure.html#body) de shinydashboard hace una explicación detallada. Dado lo anterior, la base de la aplicación sería la siguiente:

```r
ui <- dashboardPage(
    dashboardHeader(title = "Mi nuevo dashboard"),
    dashboardSidebar(disable = T),
    dashboardBody(
        fluidRow("Única fila del tablero")
    )
)

server <- function(input, output) {}
```

## Render y Output

Como se mencionó, `shiny` usa una interacción entre los objetos del servidor y la interfaz de usuario. Para ello, los tableros de visualización utilizan dos familias de funciones para conectarlos: las funciones `render_` en el servidor y `_Output` en la interfaz de usuario.

Por ejemplo, digamos que se quiere imprimir el valor de una variable definida en el servidor. No se puede llamar simplemente a la variable como lo haríamos en un script ordinario. Por el contrario, se tiene que usar esta familia de funciones de la siguiente manera:

```r
ui <- dashboardPage(
    dashboardHeader(title = "Mi nuevo dashboard"),
    dashboardSidebar(disable = T),
    dashboardBody(
        fluidRow(
            textOutput(outputId = "mi_texto")
        )
    )
)

server <- function(input, output) {
    x <- 3
    output$mi_texto <- renderText({
        paste("El valor de x es: ", x)
    })
}
```

El resultado del código anterior es un tablero así:

![ejemplo 1](/assets/img/posts/2020-08-17-como-hacer-un-dashboard-en-r/ejemplo-1.png)
*Ejemplo 1. Render Text*

Tal como lo muestra el ejemplo, el nombre que se le asigna al objeto en el servidor (el nombre que va después de `output$`) debe coincidir con el `outputId` de la función en la interfaz de usuario. De esta manera, shiny sabe cuál objeto ubicar en el tablero de visualización y en qué lugar.

### Cajas informativas

De esta misma manera podemos generar cajas informativas que contienen un valor numérico y una descripción de la información que está allí contenida. Siguiendo una forma muy similar, el siguiente código

```r
ui <- dashboardPage(
    dashboardHeader(title = "Mi nuevo dashboard"),
    dashboardSidebar(disable = T),
    dashboardBody(
        fluidRow(
            valueBoxOutput(outputId = "mi_value_box")
        )
    )
)

server <- function(input, output) {
    x <- 42
    output$mi_value_box <- renderValueBox({
        valueBox(x,
                 subtitle = "El sentido de la vida, el universo y todo lo demás"
        )
    })
}
```

genera el siguiente tablero

![ejemplo 2](/assets/img/posts/2020-08-17-como-hacer-un-dashboard-en-r/ejemplo-2.png)
*Ejemplo 2. Render Value Box*

### Gráficos

Para el caso de los gráficos, la sintaxis es muy similar. Se crea la gráfica desde el servidor usando `renderPlot` con una gráfica de ggplot2 y luego se carga en el tablero usando `plotOutput`.

```r
ui <- dashboardPage(
    dashboardHeader(title = "Mi nuevo dashboard"),
    dashboardSidebar(disable = T),
    dashboardBody(
        fluidRow(
            plotOutput(outputId = "mi_grafico")
        )
    )
)

server <- function(input, output) {
    output$mi_grafico <- renderPlot({
        ggplot(mtcars, aes(x = mpg, y = hp)) +
            geom_point()
    })
}
```

![ejemplo 3](/assets/img/posts/2020-08-17-como-hacer-un-dashboard-en-r/ejemplo-3.jpeg)
*Ejemplo 3. Render Plot*

## Tablero de covid-19

Usando este flujo de trabajo sencillo de `shiny` y `shinydashboard` puedes empezar a experimentar con tus propios tableros de visualización sobre algún tema de tu interés. En mi caso, quise hacer un tablero básico de visualización sobre los datos de covid-19 en Colombia, publicados por el Instituto Nacional de Salud y que se pueden ver en el [tablero de esa entidad](https://www.ins.gov.co/Noticias/paginas/coronavirus.aspx).

En este ejercicio usé una distribución de los elementos un poco más compleja, usando *tab boxes* y varias columnas por cada fila. Además, incluí cajas informativas, gráficos y un mapa interactivo con el paquete `leaflet`. Para ver y utilizar este tablero, puedes encontrar el código fuente y las instrucciones en el siguiente [repositorio en Github](https://github.com/camartinezbu/covid-dashboard-colombia).

## Lecturas complementarias

- [Mastering Shiny](https://mastering-shiny.org/)
- [Documentaciín de shiny](https://shiny.rstudio.com/tutorial/)
- [Documentación de shinydashboard](https://rstudio.github.io/shinydashboard/)

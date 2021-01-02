---
title: Una introducción a Jupyter
date: 2020-09-28 16:15:00 -0500
categories: [Jupyter, Tutorial]
tags: [jupyter, notebook, lab, reportes, python, r]
image: /assets/img/posts/2020-09-28-una-introduccion-a-jupyter/hola-mundo.png
excerpt: Apuesto a que no sabían que su creador es un Colombiano.
---

Al trabajar en un proyecto que requiere gran cantidad de código, llenar el archivo con comentarios a través de numerales (#) puede no ser la mejor solución para entender qué estamos haciendo o para compartirlo con otras personas. Después de todo, el archivo del código no tiene que ser el mismo de la documentación. Esto es justamente lo que buscan solucionar los cuadernos de Jupyter.

## Los Jupyter Notebooks

Según la [documentación](https://jupyter-notebook.readthedocs.io/en/stable/examples/Notebook/What%20is%20the%20Jupyter%20Notebook.html), los cuadernos de Jupyter son ambientes de computación interactiva que le permite al usuario hacer el reporte de alguna computación incluyendo código, gráficos, ecuaciones, imágenes, videos, entre otros. En cristiano, esto quiere decir que podemos explicar con detalle cada paso en el trabajo que estemos realizando y hacerlo ameno de leer para otras personas.

Y ahora un dato coctelero. El Proyecto Jupyter (que incluye los cuadernos, JupyterHub y JupyterLab) fue creado por el Colombiano [Fernando Perez](https://github.com/fperez) a partir de sus desarrollos previos en IPython.

### Instalación e inicio

La manera más fácil de instalar Jupyter es a través de [Anaconda](https://www.anaconda.com/products/individual), que es un programa de gestión de librerías para Python y R. Este incluye una instalación de Python y una serie de aplicaciones para el desarrollo de proyectos de ciencia de datos, incluyendo los cuadernos de Jupyter.

Una vez realizada la instalación de Anaconda, podemos abrir un cuaderno de Jupyter de dos maneras. La primera es abrir la la aplicación llamada Anaconda Navigator y oprimir el botón de "Launch" que acompaña el logo de Jupyter Notebook o escribir en la terminal de comandos del computador:

```bash
jupyter notebook
```

### El flujo de trabajo

Al ejecutar el código anterior, se debería abrir una pantalla en el navegador por defecto (en mi caso es Google Chrome), en el que se nos muestran las carpetas del directorio principal del computador. En la esquina superior derecha, se encuentra un botón que dice "New" que nos permitirá crear un nuevo cuaderno.

![Interfaz de Jupyter](/assets/img/posts/2020-09-28-una-introduccion-a-jupyter/interfaz-jupyter.png)
*Interfaz de Jupyter*

El trabajo en Jupyter se desarrolla a través de celdas. Así como en Excel las celdas pueden contener distintos tipos de datos, en Jupyter las celdas pueden estar en distintos *modos*: Código o Markdown. Mientras en el modo código se escribirán las instrucciones para que Jupyter ejecute, en el modo Markdown se incluirán el texto, el formato, las imágenes o el video de acompañamiento, como se muestra en la imagen anterior.

Ahora bien, además del tipo de contenido que van a tener las celdas, hay dos modos del trabajo en el cuaderno. En el modo de edición, vamos a poder modificar el contenido de cada celda según lo que queramos hacer. En el modo de comandos, vamos a poder navegar entre las celdas, crear nuevas o eliminar existentes, y definir si las celdas van a ser de código o de Markdown.

Entender los tipos de celdas y cómo navegar en el cuaderno son los únicos requerimientos para empezar a escribir cuadernos detallados y fáciles de seguir.

### Algunos atajos de teclado

Si bien la interfaz de Jupyter contiene botones para realizar la mayoría de acciones, después de trabajar con un par de cuadernos nos daremos cuenta que usar los botones no es la manera más rápida para trabajar. Estos son los atajos de teclado que uso más a menudo:

- `Enter`: Entrar al modo de edición en al celda seleccionada
- `Esc`: Salir del modo de edición y entrar al modo de comandos
- `A`: Insertar celda arriba (por la palabra Above)
- `B`: Insertar celda abajo (por la palabra Below)
- `X`: Cortar celda seleccionada
- `C`: Copiar celda seleccionada
- `V`: Pegar celdas copiadas
- `D + D`: Eliminar celda seleccionada
- `Y`: Cambiar la celda a modo de código
- `M`: Cambiar la celda a modo Markdown
- `Ctrl + Enter` o `Cmd + Enter`: Ejecutar el contenido de una celda de código

## Jupyter Lab

Jupyter Lab es la "[nueva generación de interfaz de usuario basada en la web del Proyecto Jupyter](https://jupyterlab.readthedocs.io/en/stable/)". A grandes rasgos, es una manera más refinada de interactuar con los cuadernos de Jupyter. Personalmente, prefiero trabajar con Jupyter Lab, dado que su interfaz es más intuitiva, permite interactuar más fácilmente con los demás archivos del computador y trabajar con varios cuadernos abiertos a la vez.

La buena noticia es que al instalar Anaconda también instalamos Jupyter Lab. Similar a los cuadernos tradicionales, para abrir Jupyter Lab podemos usar el Navegador de Anaconda o simplemente escribiendo en la terminal de comandos:

```bash
jupyter lab
```

## ¿Y se puede utilizar con R?

¡Claro que si! De hecho, el nombre Jupyter hace referencia a tres lenguajes de programación: **Ju**lia, **Py**thon y **R**. Y no sólo se puede trabajar con estos tres lenguajes. Una consulta a la [documentación sobre los kernels](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) de Jupyter muestra que también se encuentran soportados C#, JavaScript, Go, Ruby, PHP, entre otros.

Para trabajar con algún lenguaje distinto a Python - que es la opción por defecto al instalar Anaconda - se debe instalar el kernel correspondiente. En el caso de R, tenemos que instalar el `IRKernel` desde la terminal de comandos.

> **Nota:** Este procedimiento no funciona desde la terminal de RStudio, es necesario primero abrir R desde la terminal de comandos del computador y ahí si seguir las instrucciones.

Desde la terminal de R, vamos a escribir la siguiente línea de código para asegurarnos de que tenemos los paquetes necesarios instalados.

```r
install.packages(c('repr', 'IRdisplay', 'evaluate', 'crayon', 'pbdZMQ', 'devtools', 'uuid', 'digest'))
```

Posteriormente, vamos a instalar el `IRKernel` como tal

```r
devtools::install_github('IRkernel/IRkernel')
```

y luego la haremos visible a Jupyter al ejecutar

```r
IRkernel::installspec()
```

Y ya está. La próxima vez que abramos Jupyter, podremos seleccionar la opción para trabajar cuadernos con R, a través de la instalación local con los paquetes que ya tengamos instalados.

![Jupyter Lab con R habilitado](/assets/img/posts/2020-09-28-una-introduccion-a-jupyter/jupyter-con-r.png)
*Jupyter Lab con R habilitado*

Espero que este tutorial haya servido para motivarlos a usar los cuadernos de Jupyter por primera vez y a acercarse a Python a través de ellos. ¡Buena suerte escribiendo código!

## Lecturas complementarias

- [Documentación sobre la edición individual de Anaconda.](https://docs.anaconda.com/anaconda/)
- [Tutorial sobre el formato Markdown](https://www.markdownguide.org/basic-syntax#blockquotes-1)
- [Documentación sobre Jupyter Lab](https://jupyterlab.readthedocs.io/en/stable/)
- [¿Cómo usar Git en conjunto con Jupyter?](https://nextjournal.com/schmudde/how-to-version-control-jupyter)

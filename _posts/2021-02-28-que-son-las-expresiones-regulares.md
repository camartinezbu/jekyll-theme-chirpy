---
title: ¿Qué son las expresiones regulares?
date: 2021-02-28 11:30:00 -0500
categories: [Datos, Conceptos]
tags: [r, python, strings, regex]
image: /assets/img/posts/2021-02-28-que-son-las-expresiones-regulares/hero.jpeg
excerpt: Veamos cómo buscar patrones complejos en strings.
---

Los strings son uno de los tipos de datos más comunes que te vas a encontrar en el análisis de datos. Ya sea porque te aparecen en un dataset listo, los consigues a través de ejercicios de webscraping o porque usas un texto completo para hacer procesamiento de lenguaje natural, aprender a trabajar con strings es una herramienta clave.

Una de las herramientas más útiles para este tema son las expresiones regulares, comunmente abreviadas como *regex* por su nombre en inglés. En este post quiero darte una introducción a regex y que al finalizar tengas unas herramientas básicas para trabajar con ellas en tus análisis.

## ¿Para qué expresiones regulares?

Seguramente cuando trabajas un documento en word o estás navegando en una página web has usado la herramienta de busqueda. Al usar esta herramienta, el programa busca una correspondencia entre el término de búsqueda que pusiste y te señala las coincidencias que encontró en el documento. En algunos casos, incluso puedes señalar que no te importa si el texto está en mayusculas o minúsculas.

Si bien esta herramienta es sumamente útil, se queda corta frente algunas operaciones más avanzadas. Esto pasa porque los textos suelen tener datos no estructurados y no es una opción realista escribir manualmente todas las posibles combinaciones de caracteres que cumplan una condición dada. Por ejemplo, encontrar todas las palabras con 4 o menos caracteres en nuestro texto.

Para estos casos, las expresiones regulares te van a permitir navegar y encontrar patrones en los textos y describirlos a través de una sintaxis muy sencilla.

Ahora bien, a modo de advertencia, si bien las expresiones regulares son simples en principio , pueden volverse rápidamente tan complicadas como lo quieras hacer. Un ejemplo de esto es esta regex para [validar direcciones de correo electrónico](https://stackoverflow.com/questions/201323/how-to-validate-an-email-address-using-a-regular-expression/201378#201378).

Antes de seguir, yo tampoco entiendo en su totalidad qué hace la expresión regular de arriba. Y rara vez trabajarás con una tan compleja. Al terminar de leer este post, vamos a entender esta expresión regular un poco más sencilla para detectar direcciones de correo electrónico válidas:

```python
"^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$"
```

## ¿Cómo entran en el flujo de trabajo?

Muchos paquetes que trabajan con strings ofrecen funcionalidad para trabajar con expresiones regulares. Para el caso de R, los paquetes `stringi` o `stringr` del tidyverse permiten hacer busquedas y reemplazos basados en regex. En python, los módulos `re` y `regex` ofrecen esta funcionalidad.

Para ambos casos, las expresiones regulares se escriben a su vez en strings, por lo que deben estar dentro de comillas.

El objetivo de este post no es hacer una explicación de las funciones de estos paquetes, sino que entiendas en general su funcionamiento para utilizarlo en distintas situaciones y lenguajes de programación. En todo caso, al final encontrarás los enlaces a la documentación de estos paquetes.

## Búsquedas

Para encontrar los patrones en el texto, las regex tienen una estructura muy estricta con algunos caracteres especiales. Vamos a ver cómo funcionan las búsquedas básicas y luego algunas funciones más avanzadas.

### a. Búsquedas básicas

Para búsquedas muy básicas, las expresiones regulares funcionan de la misma manera que la herramienta de búsqueda en tu navegador o procesador de texto. Es decir, un regex de la forma `test` va a hacer match con todas las ocurrencias de este string en el texto.

### b. Anclas

Las anclas son la primera herramienta clave para las regex. Usando los símbolos `^` y `$` vamos a indicar si queremos que busque los caracteres dependiendo de si están al inicio o al final del string.

| Expresión | Efecto
| ---- | ----
| `^El` | selecciona cualquier string que *empiece* con The
| `fin$` | selecciona cualquier string que *termine* con end
| `^El fin$` | selecciona *exactamente* este string

### c. Cuantificadores

Ahora bien, hasta ahora tenemos que escribir exactamente el número de caracteres que queremos encontrar. Con los símbolos `*`, `+`, `+`, `{}` vamos a poder indicar si hay repetición de caracteres, como lo muestra la siguiente tabla:

| Expresión | Efecto
| ---- | ----
| `abc*` | selecciona un string que tenga ab *seguido por cero o más c*
| `abc+` | selecciona un string que tenga ab *seguido por una o más c*
| `abc?` | selecciona un string que tenga ab *seguido por cero o una c*
| `abc{2}` | selecciona un string que tenga ab *seguido por exactamente dos c*
| `abc{2,}` | selecciona un string que tenga ab *seguido por dos o más c*
| `abc{,4}` | selecciona un string que tenga ab *seguido por hasta cuatro c*
| `abc{2,5}` | selecciona un string que tenga ab *seguido por de 2 a 5 c*

Como puedes ver, estos simbolos sólo aplican para el caracter que está justo antes en la expresión regular. Para indicarle a la expresión regular que queremos que el cuantificador aplique para más caracteres, tenemos que usar paréntesis `()`. A modo de ejemplo:

| Expresión | Efecto
| ---- | ----
| `a(bc)*` | selecciona un string que tenga a *seguido por cero o más copias de la secuencia bc*
| `a(bc){2,5}` | selecciona un string que tenga a *seguido por de 2 a 5 copias de bc*

### d. Operador o

Tal como si estuvieramos armando un condicional, podemos indicarle a la expresión regular si queremos que un string encuentre en una posición dada un caracter u otro.

Para operaciones sencillas, usamos el caracter de línea vertical `|`, pero si tenemos que encadenar varios caracteres podemos usar los paréntesis cuadrados `[]` para abreviar. Adicionalmente, si escribimos al inicio de los paréntesis cuadrados el símbolo `^`, es como si aplicaramos una negación.

| Expresión | Efecto
| ---- | ----
| `ab(c|d)` | selecciona *tanto el string abc como abd*
| `ab[cdef]` | selecciona *los strings abc, abd, abe y abf*
| `ab[^cdef]` | selecciona *los strings que empiecen con ab seguidos por un caracter, excepto abc, abd, abe y abf*

### e. Clases de caracteres

Así como los cuantificadores nos permiten especificar la búsqueda con respecto al número de caracteres, existe una sintaxis especial para buscar por el tipo de caracteres. Estos se describen a continuación:

| Expresión | Efecto
| ---- | ----
| `\d` | selecciona *un dígito*
| `\w` | selecciona *un caracter de palabra* (caracter alfanumérico o guión bajo)
| `\s` | selecciona *un caracter de espacio* (incluye espacio, tab y nueva línea)
| `.` | selecciona *cualquier caracter*

Tal como cuando usabamos `[]` teníamos una negación, acá también existe. Para ello, escribimos estos mismos términos pero en mayúscula.

| Expresión | Efecto
| ---- | ----
| `\D` | selecciona *cualquier caracter excepto un dígito*
| `\W` | selecciona *cualqueir caracter excepto un caracter de palabra* (caracter alfanumérico o guión bajo)
| `\S` | selecciona *cualquier caracter excepto un caracter de espacio* (incluye espacio, tab y nueva línea)

Finalmente, vale la pena decir que hay algunas abreviaciones usando los paréntesis cuadrados que hacen match con ciertos grupos de caracteres más específicos:

| Expresión | Efecto
| ---- | ----
| `[a-z]` | selecciona *una letra en minúcula*
| `[A-Z]` | selecciona *una letra en mayúscula*
| `[0-9]` | selecciona *un número de 0 a 9*

## "Escapando" caracteres

Como te puedes dar cuenta, las regex usan una gran cantidad de símbolos para buscar ciertos patrones en el texto: `^ $ * + ? {} [] \ .`. Sin embargo, ¿qué sucede cuando queremos buscar específicamente estos caracteres en el texto?

La respuesta es que tenemos que usar el `\` para *escapar* a la funcionalidad en expresiones regulares. Por ejemplo, si quiero buscar los puntos en un texto y no cualquier caracter, la expresión regular correspondiente es `\.`.

Esto también implica que para efectos prácticos del uso de las clases de caracteres, no vamos a escribir `\d` sino `\\d` dentro del string.

## El ejemplo de los correos electrónicos

Habiendo visto lo anterior, volvamos a la expresión regular para la detección de correos electrónicos:

```python
"^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$"
```

Si bien antes parecía algo muy extraño, ya te puedes dar cuenta que en realidad es algo muy sencillo. Separemos la expresión regular en 3 partes para ver qué hace:

- `^[a-zA-Z0-9_.+-]+`: Ubica los strings que empiecen por y tengan al menos una letra en mayúscula o minúscula, un número, un guión bajo, un punto, un más o un menos.
- `@[a-zA-Z0-9-]+`: Seguidos de un arroba y al menos una letra en mayúscula o minúscula, un dígito o un guión.
- `\.[a-zA-Z0-9-.]+$`: Seguidos de un punto y que terminen en al menos una letra en mayúscula o minúscula, un dígito o un guión.

Como te puedes dar cuenta, una dirección cualquiera como *este_es_un_email_inventado@proveedor.net* aparecería como resultado de la búsqueda al usar esta expresión regular.

Al combinar todos los elementos descritos con anterioridad, puedes definir búsquedas muy poderosas que te van a facilitar el procesamiento de textos.

## Lecturas relacionadas

Ya conoces el funcionamiento básico de las expresiones regulares. Te invito a leer sobre la aplicación de las expresiones regulares en R y en python, así como algunos temas más avanzados como agrupaciones y los conceptos de *lazy* y *greedy* en los siguientes enlaces:

- [El capítulo dedicado a strings](https://r4ds.had.co.nz/strings.html#matching-patterns-with-regular-expressions) del libro *R for Data Science*.
- [La documentación stringr](https://stringr.tidyverse.org/articles/regular-expressions.html).
- [La documentación de stringi](https://stringi.gagolewski.com/).
- [La documentación de re](https://docs.python.org/3/library/re.html).
- [La documentación de regex](https://pypi.org/project/regex/).

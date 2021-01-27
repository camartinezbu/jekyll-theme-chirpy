---
title: Estructuras de datos básicas en python
date: 2021-01-24 10:45:00 -0500
categories: [Python, Tutorial]
tags: [listas, tuplas, diccionarios, sets, python, arrays]
image: /assets/img/posts/2021-01-24-estructuras-de-datos-basicas-en-python/pexels-pixabay-159519.jpg
excerpt: Hablemos de listas, tuplas, diccionarios y conjuntos.
---

Una de las primeras cosas que tienes que saber si quieres comenzar tu camino para programar en python son los diferentes tipos de estructuras de datos que tendrás a tu disposición. Cada una de ellas cuenta con características particulares, de tal manera que no hay una sóla que sea la solución a todos los problemas. Al finalizar este post tendrás las herramientas básicas para entender estas diferencias, crear estas estructuras y trabajar con ellas.

## Listas

Una lista es una estructura de datos que puede contener elementos de distintos tipos: números, strings, booleanos, etc. aunque usualmente se usa para contener solamente uno. Es importante saber que las listas son **mutables**, es decir que se pueden modificar los elementos que estas contienen después de que la lista se creó y que los elementos tienen un **índice numérico**. Entraré en más detalle sobre esto cuando veamos cómo acceder a sus elementos.

### a. Crear una lista

Para crear una lista en python, basta con escribir una secuencia de elementos separados por comas dentro de paréntesis cuadrados. A manera de ejemplo:

```python
# Crear una lista de un elemento
mi_lista1 = [4]

# Crear una lsita de varios elementos
mi_lista2 = ['Camilo', 'Martinez', 'Ciencia', 'Datos']
```

### b. Acceder a los elementos de una lista

Como mencionaba con anterioridad, las listas están indexadas de manera numérica de conformidad con la posición de cada elemento de la lista. Esto es lo que permite acceder a los elementos de manera ordenada cuando estemos trabajando.

> **Nota:**: Aquí es muy importante decir que en Python se empieza contando desde 0. Es decir, el primer elemento de una lista tendrá un índice 0, el segundo tendrá un índice 1 y así sucesivamente.

Supongamos que quiero acceder al string `Ciencia` en `mi_lista2` del ejemplo anterior. Sabemos que `Ciencia` está en la tercera posición en la lista, lo que implica que el índice numérico que debemos usar es el 2. Entonces, para acceder a este elemento simplemente debemos escribir en python:

```python
mi_lista2[2]
```

Estos paréntesis cuadrados que van después de una estructura de datos, se conocen como el **Operador de subíndice** (o *Subscript operator* en inglés). Hay muchas formas de jugar con este operador de subíndice; no necesariamente tendemos que extraer los elementos uno por uno. Por ejemplo, si queremos extraer `Martínez` y `Ciencia`, podemos usar un **slice** de la siguiente manera:

```python
mi_lista2[1:3]
```

Te estarás preguntando, pero si `Ciencia` era el elemento 2 ¿por qué hay que poner 3 en el número de la derecha? Así como python cuenta desde 0 y no desde 1, cuando se hacen slices el número de la derecha es 1 + el índice que queremos. Supongamos que tenemos una lista con 20 elementos y quiero extraer el 6 al 15. Por la manera como está escrito python, tendríamos que escribir en el índice de la derecha 15+1, es decir, 16: 

```python
mi_lista3[6:16]
```

Por otro lado, recuerda que las listas son mutables, por lo que podemos modificar los elementos que están en la lista usando la misma forma de acceder a los elementos descrita anteriormente. Si por ejemplo quisiera modificar el `Martínez` por `Rodríguez` lo puedo hacer escribiendo:

```python
mi_lista_2[1] = 'Rodríguez'
```

Encontrarás más información sobre esta forma de extraer y modificar elementos en [este link](https://www.w3schools.com/python/python_lists_access.asp).

### c. Métodos útiles de las listas

No voy en entrar en detalle en este post, pero es importante que sepas que Python es un lenguaje de programación **orientado a objetos**, un paradigma de la programación basado en el concepto de los **objetos**. Estos objetos pueden contener tanto datos como código y en términos generales tienen **atributos**, que son las características del objeto, y **métodos**, que son las acciones asociadas a éste. A continuación, algunos métodos útiles para trabajar con listas:

```python
# Añadir un elemento al final de una lista
mi_lista.append('Nuevo elemento')

# Eliminar todos los elementos de una lista
mi_lista.clear()

# Encontrar el índice de un elemento de la lista
mi_lista.index('Elemento de la lista')

# Reversar el orden de la lista
mi_lista.reverse()

# Ordenar la lista
mi_lista.sort()

# Contar cuántos elementos de la lista tienen un valor dado
mi_lista.count('Valor para contar')
```

## Tuplas

Las tuplas son similares a las listas, en la medida que pueden almacenar elementos de distintos tipos y usan índices numéricos, pero a diferencia de ellas **no son mutables**.

### a. Crear una tupla

La creación de una tupla es muy similar a la de una lista. En vez de usar paréntesis cuadrados usamos paréntesis normales. Ahora bien, para el caso de una tupla de un elemento, la creación es un poco distinta porque si sólo se encierra en paréntesis, python lo va a interpretar como un número, un string o un booleano común y corriente. Entonces, para crear una tupla escribimos:

```python
# Crear tuplas de un elemento
mi_tupla1 = (8,) # En este caso, siempre tenemos que escribir una coma después del único elemento

# Crear tuplas de varios elementos
mi_tupla2 = ('Manzanas', 'Peras', 'Uvas')
```

### b. Acceder a los elementos de una tupla

Esta operación sigue exáctamente las mismas reglas que el acceso a elementos en las listas. Para saber qué elemento queremos obtener tenemos que encontrar su posición en la tupla contando desde 0 y usar el operador de subíndice para elegirlo. Por ejemplo, si quiero seleccionar `Manzanas` de `mi_tupla2`, tengo que escribir:

```python
mi_tupla2[0]
```

Como decía con anterioridad, las listas no son mutables, por lo que no las puedes modificar directamente. Es decir, un método como `.append()` no funcionaría en este caso. Sin embargo, si quieres crear una nueva tupla con elementos adicionales a la inicial puedes usar el operador `+=`. Por ejemplo, si quiero añadir fresas y duraznos a la `mi_tupla2`, puedo escribir:

```python
mi_tupla2 += ('Fresas', 'Duraznos')
```

En estricto sentido, lo que hace esta línea de código no es *modificar* la tupla anterior, sino crear una *nueva tupla* que ahora contiene 5 elementos y *ponerle el nombre* de la tupla anterior. Puede parecer una diferencia muy pequeña pero cobrará importancia en la medida que trabajes más con éstas estructuras.

### c. Métodos útiles de las tuplas

Al no ser mutables como las listas, las tuplas tienen menos métodos con los que puedes trabajar. Básicamente encontrarás dos métodos:

```python
# Encontrar el índice de un elemento de la lista
mi_tupla.index('Elemeto de la tupla')

# Contar cuántos elementos de la lista tienen un valor dado
mi_tupla.count('Valor para contar')
```

## Diccionarios

Los diccionarios son estructuras un poco distintas, que funcionan relacionando **llaves** (o *keys*) con **valores** de manera **no ordenada** (o más exactamente no tienen un índice numérico). En los diccionarios, las llaves deben ser inmutables (por ejemplo strings o tuplas) y deben ser únicas, aunque los diccionarios como tal si son mutables.

### a. Crear un diccionario

Para crear un diccionario vamos a usar corchetes para encerrar parejas de elementos separados por ':' de la siguiente manera. El primer elemento va a ser la llave y el segundo elemento va a ser el valor.

```python
mi_diccionario = {'Bogotá': 11001 ,'Medellín': 05001, 'Cali': 76001}
```

En este caso, creé un diccionario con el código DIVIPOLA de éstas tres ciudades, disponible [aquí](https://geoportal.dane.gov.co/geovisores/territorio/consulta-divipola-division-politico-administrativa-de-colombia/).

### b. Acceder a los elementos de un diccionario

El operador de subíndice también funciona para los diccionarios, sólo que en este caso no vamos a incluir un índice numérico sino el **nombre de la llave** que queremos obtener. Por ejemplo, si quiero saber cuál es el código DIVIPOLA de Medellín, voy a escribir:

```python
mi_diccionario['Medellín']
```

Ahora, supongamos que se me olvidó añadir a la ciudad de Bucaramanga y quisiera que estuviera incluida en mi diccionario. Para ello, hacemos una operación similar a la modificación de las listas que se describió con anterioridad:

```python
mi_diccionario['Bucaramanga'] = 68001
```

Este mismo procedimiento lo podemos usar si nos equivocamos al introducir un valor en el diccionario (p.e. Si el código DIVIPOLA de Cali estuviera mal).

Con esto, te debe quedar un poco más claro por qué los diccionarios se llaman así.

### c. Métodos útiles de los diccionarios

Algunos métodos que debes saber para trabajar con diccionarios son:

```python
# Eliminar todos los elementos de un diccionario
mi_diccionario.clear()

# Obtener una lista con todas llaves del diccionario
mi_diccionario.keys()

# Obtener una lista con todos los valores del diccionario
mi_diccionario.values()

# Obtener una lista de tuplas con cada llave y cada valor
mi_diccioanrio.items()

# Eliminar una pareja de llave-valor
mi_diccionario.pop('Nombre de la llave')
```

## Conjuntos

Finalmente, los conjuntos son colecciones **no ordenadas** de **valores únicos** y sólo pueden contener elementos inmutables como strings, números o tuplas.

### a. Crear un conjunto

Para crear un conjunto también usamos los corchetes, pero en vez de incluir parejas de llaves y valores separados por ':', vamos a escribir los elementos a incluir de la siguiente manera:

```python
mi_conjunto1 = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
```

Vale la pena decir, que también podemos crear conjuntos a partir de listas usando la función `set()`. Por ejemplo:

```python
mi_lista_para_conjunto = [1, 2, 2, 2, 3, 5, 7, 9]

mi_conjunto2 = set(mi_lista_para_conjunto)
```

En este caso vale la pena aclarar que si bien la lista tiene elementos repetidos, al crear el conjunto sólo apareceran los elementos una vez. Para este ejemplo, el conjunto estára compuesto por `{1, 2, 3, 5, 7, 9}`.

### b. Acceder a los elementos de un conjunto

En vez de acceder a los elementos tal cual como lo hemos hablado para las estructuras de datos anteriores, al ser estructuras no ordenadas y de valores únicos, lo que podemos hacer con los conjuntos es verificar si un elemento está dentro del conjunto de la siguiente manera:

```python
1 in mi_conjunto1

11 not in mi_conjunto 1
```

El resultado de la operación anterior será un booleano, es decir, un `True` o un `False`.


### c. Métodos útiles de los conjuntos

Tal como en la teoría de conjuntos que se ve en el colegio, las operaciones más interesantes de los conjuntos se dan cuando se interactúan varios conjuntos entre si. Aquí una lista de métodos útiles para conjuntos:

```python
# Añadir un elemento al conjunto
mi_conjunto1.add('Elemento a añadir')

# Eliminar un elemento del conjunto
mi_conjunto.discard('Elemento a eliminar')
mi_conjunto.remove('Elemento a eliminar')

# Eliminar todos los elementos del conjunto
mi_conjunto.clear()

# Encontrar la unión de dos conjuntos
mi_conjunto.union(otro_conjunto)

# Encontrar la intersección de dos conjuntos
mi_conjunto.intersection(otro_conjunto)

# Encontrar si un conjunto contiene a otro conjunto
mi_conjunto.issuperset(otro_conjunto)

# Encontrar si un conjunto está contenido en otro conjunto
mi_conjunto.issubset(otro_conjunto)

# Encontrar si dos conjuntos son disjuntos
mi_conjunto.isdisjoint(otro_conjunto)
```

Espero que te haya servido esta guía para entender un poco mejor los diferentes tipos de estructuras de datos en python. Si quieres leer más del tema, también te recomiendo leer sobre los [arrays de numpy](https://numpy.org/doc/stable/reference/generated/numpy.array.html), una estructura que trae algunas ventajas sobre las lsitas y que es la base de paquetes para ciencia de datos como pandas.
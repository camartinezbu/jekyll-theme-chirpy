---
title: ¡Cuidado con los decimales!
date: 2020-10-05 16:00:00 -0500
categories: [Computacion, Matematicas]
tags: [float, numeros, python, integer, decimal]
image: /assets/img/posts/2020-10-05-cuidado-con-los-decimales/thumbnail-calculator.jpg
excerpt: Los computadores nunca se equivocan en matemáticas... ¿verdad?
math: true
---

En general, usamos a los computadores para realizar cálculos que serían más complicados si los hacemos de manera manual. Tanto por la capacidad de los computadores de realizar muchas tareas repetitivas en menor tiempo, como por la menor posibilidad de introducir errores en las operaciones. Por ejemplo, oprimir el botón equivocado en la calculadora.

Sin embargo, en lo que respecta a números decimales, la manera en cómo se procesan en muchos lenguajes de programación puede llevar a que algunas operaciones no se comporten como esperamos.

## El problema

Para explicar lo que no funciona, vamos a desarrollar un ejercicio muy sencillo. En una consola de python, realizar la siguiente operación

```python
1 + 2
```

nos va a devolver 3. Esto es evidente, es una suma simple.

Ahora bien, introduzcamos los números decimales al dividir la operación anterior entre 10. Es decir, sumemos

```python
0.1 + 0.2
```

¿Debería dar 0.3 verdad? No exactamente. En realidad, python va a arrojar que la respuesta a esta suma es 0.30000000000000004. Claramente algo debe estar pasando tras bambalinas.

### Una particularidad para R

Si bien este problema no se presenta en un entorno de R, no significa que no existan dificultades con los decimales. Si no me crees, intenta resolver  

```r
ceiling(31/60 * 60)
```

Teniendo en cuenta que la función `ceiling` busca el número entero más pequeño que sea mayor o igual al argumento, esta expresión debería ser igual a 31 ¿cierto?

## Floats

La manera como python y la mayoría de lenguajes de programación representan los números decimales es a través de los números de punto flotante, o de manera abreviada *floats*.

### Notación científica

En el fondo, lo que hacen los floats es representar cualquier número en términos de notación científica. Es decir, separan el número en un *significando* y un *exponente*.

El *significando* contiene los dígitos del número en cuestión y los almacena como un número con un sólo lugar en las unidades y cuántos decimales sean necesarios. A modo de ejemplo. Para el número 117, el significando sería 1.17.

Por su parte, la parte del *exponente* indica en dónde va en realidad el punto decimal del número original. En otras palabras, por qué potencia de 10 tenemos que multiplicar el signficando para volver al número original. Para el mismo ejemplo, el exponente sería 2, pues al multiplicar 1.17 por 100, que es \\(10^2\\), volvemos a obtener 117.

Esto también aplica para números negativos o números muy pequeños. Por ejemplo, si queremos representar -0.0005 en esta notación, tenemos que el significando es -5 y que el exponente es -4, pues al resolver \\(-5 * 10^{-4}\\) volvemos al número original.

### Precisión

Ahora bien, con todo esto no hay que perder de vista que los computadores almacenan los números en sistema binario. Es decir, en potencias de 2. Esto hace que la parte decimal de los números sea el resultado de sumar fracciones del estilo \\(1/2 + 1/4 + 1/8 + 1/16 + ...\\).

Dado lo anterior, es muy fácil representar 0.5 y de hecho, para este número no hay problema en la suma de fracciones. Sin embargo, no todos los números se pueden representar fácilmente de esta forma. Volviendo al caso inicial, no podemos escribir ni 0.1 ni 0.2 con una suma finita de potencias de 2. Entonces, lo que hace el computador es tratar de aproximar lo máximo posible al número deseado.

Por esta razón, cuando insertamos en la consola de python

```python
0.1 + 0.2
```

lo que hace el interpretador es procesar ambos números como floats y tratar de resolver la suma con la mejor aproximación posible teniendo en cuenta el sistema binario.

Como resultado, lo que devuelve python es un valor muy cercano a 0.3, pero no exactamente igual.

## ¿Qué hacer al respecto?

### Decimals

En este caso, y en especial cuando tenemos que lidiar con cálculos de dinero y no nos podemos permitir estos pequeños errores, python incluye un tipo de número llamado `Decimal` en el módulo `decimal`. 

En el siguiente cuadro de código está la comparación de cómo escribimos un *float* y un número con esta nueva clase.

```python
from decimal import Decimal

# 0.1 como float
0.1

#0.3 como Decimal
Decimal('0.1')
```

Al operar usando los `Decimal` vamos a tener que el problema inicial desaparece, y python va a resolver correctamente la operación de 0.1 + 0.2 y dará 0.3 cerrado.

### O... ¿nada?

En la gran mayoría de casos, este tipo de problemas con los floats son demasiado pequeños como para que nos tengamos que preocupar. Es decir, ¿en realidad importa para todos nuestros cálculos una diferencia que está casi 20 ceros a la derecha del punto?

Esto dependerá de la aplicación puntual que estemos desarrollando. Pero lo importante es estar consciente de que la mayoría de lenguajes de programación representan y almacenan los decimales de esta forma  para saber en qué casos va a representar un problema y actuar de manera correspondiente.

En este sentido, no es que hayamos vivido varias décadas con computadores que no saben sumar. Es que la forma en cómo se almacenan estos números no es tan intuitiva. Entonces, la próxima vez que vayas a hacer operaciones con decimales, presta mucha atención a cómo funcionan las floats y así te ahorrarás dolores de cabeza.

## Lecturas complementarias

- [The floating point guide](https://floating-point-gui.de/)
- [Floating point numbers - Computerphile](https://www.youtube.com/watch?v=PZRI1IfStY0)
- [Why doesn’t R think these numbers are equal?](https://cran.r-project.org/doc/FAQ/R-FAQ.html#Why-doesn_0027t-R-think-these-numbers-are-equal_003f)

---
modulo: 1. Fundamentos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[_Overview|← Fundamentos]] | [[Home|← Inicio]]

# Transformaciones Homogéneas

> Forma matricial unificada de **rotación + traslación** (rototraslación). Codifica una **pose** completa y permite **componer** transformaciones encadenadas — la pieza central para describir la posición y orientación de un robot, sus sensores y los objetos que percibe.

## Prerequisitos
- [[Rotaciones]]
- [[Matrices]] (producto, inversa)

## 1. Rototraslación  *(Teóricas 01-algebra_lineal, slides 32–33)*

Una rototraslación 2D combina una rotación $R$ y una traslación $\mathbf{d}$:

$$
{}^A\mathbf{p} = {}^A\mathbf{d} + {}^A_B R \cdot {}^B\mathbf{p}
$$

Para no andar separando rotación y traslación, se las **embebe en una única matriz** agregando una fila $[0, \dots, 0, 1]$ y una coordenada extra "1" al vector. Esa es la idea de las **transformaciones homogéneas**:

$$
\begin{pmatrix} {}^A\mathbf{p} \\ 1 \end{pmatrix} = \begin{pmatrix} {}^A_B R & {}^A\mathbf{d} \\ \mathbf{0}_{1\times 2} & 1 \end{pmatrix} \begin{pmatrix} {}^B\mathbf{p} \\ 1 \end{pmatrix}
$$

![[Rototraslacion 2D.png]]
*Rototraslación 2D y su forma como transformación homogénea. Slide 32.*

![[Rototraslacion - homogeneas.png]]
*Forma matricial completa de la rototraslación 2D y su forma homogénea compacta. Slide 33.*

## 2. Transformaciones afines como matrices  *(Teóricas 01-algebra_lineal, slide 34)*

Una transformación afín en 2D o 3D se describe con una matriz que contiene:

$$
{}^A\mathbf{T}_B = \begin{pmatrix} {}^A_B R & {}^A\mathbf{d} \\ \mathbf{0} & 1 \end{pmatrix}
$$

- $R$ es la **matriz de rotación**.
- $\mathbf{d}$ es el **vector de traslación**.
- La forma homogénea **conserva el orden** de las transformaciones (no conmutatividad respetada).

![[Transformacion afin - composicion matriz.png]]
*Estructura de una transformación afín: bloque de rotación + vector de traslación. Slide 34.*

## 3. Composición e inversa  *(Teóricas 01-algebra_lineal, slide 35)*

**Composición** — el producto de dos transformaciones homogéneas es otra transformación homogénea:

$$
T_1 T_2 = \begin{pmatrix} R_1 & \mathbf{t}_1 \\ \mathbf{0} & 1 \end{pmatrix} \begin{pmatrix} R_2 & \mathbf{t}_2 \\ \mathbf{0} & 1 \end{pmatrix} = \begin{pmatrix} R_1 R_2 & \mathbf{t}_1 + R_1 \mathbf{t}_2 \\ \mathbf{0} & 1 \end{pmatrix}
$$

**Inversa** — sin necesidad de invertir el bloque entero:

$$
T^{-1} = \begin{pmatrix} R & \mathbf{t} \\ \mathbf{0} & 1 \end{pmatrix}^{-1} = \begin{pmatrix} R^T & -R^T \mathbf{t} \\ \mathbf{0} & 1 \end{pmatrix}
$$

(usa que la inversa de una rotación es su transpuesta).

![[Transformacion afin - composicion e inversa.png]]
*Fórmulas de composición e inversa de transformaciones homogéneas. Slide 35.*

![[Composicion - sistemas multiples.png]]
*Diagrama con múltiples sistemas (mundo, cámara fija, robot, cámara sobre robot, objeto) conectados por transformaciones homogéneas. Slide 36.*

## 4. Pose de un robot  *(Teóricas 01-algebra_lineal, slide 37)*

La **pose** (posición + orientación) de un robot es exactamente una transformación homogénea. Dado un vector posición ${}^W\mathbf{p}$ y una matriz de orientación ${}^W\mathbf{O}_B$ del robot $B$ en el mundo $W$:

$$
{}^W\boldsymbol{\xi}_B = \begin{pmatrix} {}^W\mathbf{O}_B & {}^W\mathbf{p} \\ \mathbf{0} & 1 \end{pmatrix}
$$

La pose **toma elementos en coordenadas del robot y los devuelve en coordenadas del mundo**.

![[Pose de un robot.png]]
*La pose como transformación homogénea entre el sistema del robot y el sistema del mundo. Slide 37.*

## 5. Combinando transformaciones  *(Teóricas 01-algebra_lineal, slides 38–40)*

Caso de uso típico: el robot tiene un sensor montado encima, y el sensor percibe un objeto. ¿Dónde está el objeto en coordenadas del mundo?

- Matriz $A$ — pose del robot en el mundo (mundo → robot).
- Matriz $B$ — posición del sensor sobre el robot (robot → sensor).
- Vector $\mathbf{p}$ — ubicación del objeto en la terna del sensor.

![[Combinando transformaciones - planteo.png]]
*Planteo: el sensor percibe un objeto en su propio sistema, sin saber su ubicación absoluta. Slide 38.*

Encadenando transformaciones:
- $B\mathbf{p}$ da la pose del objeto **respecto al robot**.
- $AB\mathbf{p}$ da la pose del objeto **respecto al mundo**.

![[Combinando transformaciones - resultado.png]]
*Encadenamiento $A B \mathbf{p}$ que lleva el objeto desde la terna del sensor hasta la terna del mundo. Slide 40.*

Este patrón **mundo → robot → sensor → objeto** (y sus variantes) aparece en todo lo que sigue: localización, mapeo, fusión de sensores.

## 6. Variantes y conexiones
- Caso degenerado sin rotación → traslación pura.
- Caso degenerado sin traslación → [[Rotaciones|rotación pura]].
- La inversa permite ir **mundo → robot** (en vez de robot → mundo).
- Aplicación directa en [[2. Locomoción/_Overview|locomoción]] (pose del robot que cambia con el tiempo) y [[3. Sensores y Modelos/_Overview|sensores]] (coordenadas relativas).

## Fuentes
- `Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf` — slides 32–40.

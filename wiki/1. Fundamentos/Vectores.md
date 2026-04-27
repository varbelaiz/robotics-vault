---
modulo: 1. Fundamentos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Fundamentos|← Fundamentos]] | [[Robotica|← Inicio]]

# Vectores

> Arreglo ordenado de números que representa un punto (o dirección) en un espacio de dimensión $n$.

## Prerequisitos
- Ninguno — primer ladrillo del módulo.

## 1. Definición y representación  *(Teóricas 01-algebra_lineal, slide 2)*

Un vector $\mathbf{a}$ es simplemente un arreglo de números:

$$
\mathbf{a} = \begin{pmatrix} a_1 \\ a_2 \\ \vdots \\ a_n \end{pmatrix}
$$

Geométricamente, un vector representa **un punto en un espacio de dimensión $n$** (o equivalentemente, una flecha desde el origen hasta ese punto).

![[Vector - definicion.png]]
*Vector como arreglo de números y como punto en el plano. Slide 2.*

## 2. Operaciones básicas  *(Teóricas 01-algebra_lineal, slide 3)*

- **Producto por escalar** — escala la magnitud sin cambiar la dirección.
- **Suma de vectores** — componente a componente.
- **Producto interno** — devuelve un escalar (sección 3).
- **Norma** — devuelve la longitud.

## 3. Producto interno  *(Teóricas 01-algebra_lineal, slide 4)*

Dados dos vectores $\mathbf{a}, \mathbf{b}$ del mismo tamaño:

$$
\mathbf{a} \cdot \mathbf{b} = \mathbf{b} \cdot \mathbf{a} = \sum_i a_i b_i
$$

Es **conmutativo** y **devuelve un escalar**.

**Interpretación geométrica clave**: si $\|\mathbf{a}\| = 1$, entonces $\mathbf{a} \cdot \mathbf{b}$ es la **longitud de la proyección de $\mathbf{b}$ sobre la dirección de $\mathbf{a}$**.

![[Vector - producto interno.png]]
*Producto interno geométrico: proyección de $\mathbf{b}$ sobre $\mathbf{a}$ unitario, y caso ortogonal. Slide 4.*

**Ortogonalidad**: dos vectores son ortogonales sii $\mathbf{a} \cdot \mathbf{b} = 0$.

## 4. (In)Dependencia lineal  *(Teóricas 01-algebra_lineal, slides 5–6)*

Un vector $\mathbf{b}$ es **linealmente dependiente** de un conjunto $\{\mathbf{a}_1, \mathbf{a}_2, \dots, \mathbf{a}_n\}$ si existen coeficientes $\{k_i\}$ tales que:

$$
\mathbf{b} = \sum_i k_i \mathbf{a}_i
$$

Es decir, si $\mathbf{b}$ se puede obtener combinando los $\mathbf{a}_i$ escalados.

Si **no existen** tales coeficientes, $\mathbf{b}$ es **linealmente independiente** del conjunto.

![[Vector - dependencia lineal.png]]
*Vector $\mathbf{b}$ obtenido como combinación lineal de $\mathbf{a}_1$ y $\mathbf{a}_2$ con coeficientes $k_1, k_2$. Slide 6.*

## 5. Variantes y conexiones
- Caso particular de [[Matrices]] — un vector de dimensión $d$ es equivalente a una matriz $d \times 1$.
- La independencia lineal es el concepto base para [[Matrices|rango]] de una matriz.

## Fuentes
- `Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf` — slides 2–6.

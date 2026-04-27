---
modulo: 1. Fundamentos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Fundamentos|← Fundamentos]] | [[Robotica|← Inicio]]

# Matrices

> Arreglo bidimensional de números. Codifica transformaciones lineales y sistemas de ecuaciones; es el lenguaje básico de toda la robótica.

## Prerequisitos
- [[Vectores]]

## 1. Definición

Una matriz $\mathbf{A}$ de tamaño $n \times m$ es un arreglo de números con dos índices:

$$
\mathbf{A} = \begin{pmatrix}
a_{11} & a_{12} & \cdots & a_{1m} \\
a_{21} & a_{22} & \cdots & a_{2m} \\
\vdots & & & \vdots \\
a_{n1} & a_{n2} & \cdots & a_{nm}
\end{pmatrix}
$$

- **1er índice** = **fila**.
- **2do índice** = **columna**.
- Notación de tamaño: $A : n \times m$.
- Un vector de dimensión $d$ es equivalente a una matriz $d \times 1$.

![[Matriz - definicion.png]]
*Matriz $n \times m$ con su convención de índices. Slide 7.*

## 2. Matriz como colección de vectores

Una misma matriz puede mirarse como:

- Una colección de **vectores columna** $\mathbf{a}_{*1}, \mathbf{a}_{*2}, \dots, \mathbf{a}_{*m}$.
- Una colección de **vectores fila** $\mathbf{a}_{1*}^T, \mathbf{a}_{2*}^T, \dots, \mathbf{a}_{n*}^T$.

Esta dualidad es fundamental para entender los productos.

![[Matriz - vectores columna.png]]
*Matriz vista como colección de vectores columna. Slide 8.*

![[Matriz - vectores fila.png]]
*Matriz vista como colección de vectores fila. Slide 9.*

## 3. Operaciones matriciales

- Multiplicación por un escalar.
- **Suma** — conmutativa, asociativa.
- Multiplicación por un vector (sección 4).
- **Producto** — **no conmutativo** (sección 5).
- **Inversión** — sólo para matrices cuadradas de rango completo (sección 7).
- **Transposición**.

## 4. Producto matriz-vector

Dadas $\mathbf{A}$ ($n \times m$) y $\mathbf{b}$ ($m \times 1$), $\mathbf{Ab}$ es un vector de tamaño $n \times 1$. Hay **dos interpretaciones equivalentes**:

1. **Vista por filas** — el componente $i$ de $\mathbf{Ab}$ es el producto interno entre la fila $i$ de $\mathbf{A}$ y $\mathbf{b}$:

$$
(\mathbf{Ab})_i = \mathbf{a}_{i*}^T \cdot \mathbf{b}
$$

2. **Vista por columnas** — $\mathbf{Ab}$ es una **combinación lineal de las columnas** de $\mathbf{A}$ con coeficientes $\{b_i\}$:

$$
\mathbf{Ab} = \sum_k \mathbf{a}_{*k} b_k
$$

![[Producto matriz-vector.png]]
*Las dos interpretaciones del producto matriz-vector: producto interno por filas y combinación lineal por columnas. Slide 11.*

**Interpretación geométrica**: si las columnas de $\mathbf{A}$ representan un sistema de referencia, entonces $\mathbf{Ab}$ calcula la **transformación global de $\mathbf{b}$** según ese sistema.

![[Producto matriz-vector geometrico.png]]
*Interpretación geométrica: $\mathbf{Ab}$ como suma vectorial de las columnas escaladas. Slide 12.*

## 5. Producto de matrices

$\mathbf{C} = \mathbf{AB}$ extiende naturalmente el producto matriz-vector. Cada elemento $c_{ij}$ es el producto interno entre la fila $i$ de $\mathbf{A}$ y la columna $j$ de $\mathbf{B}$. Equivalentemente, **cada columna de $\mathbf{C}$ es la transformación de la columna correspondiente de $\mathbf{B}$ según $\mathbf{A}$**:

$$
\mathbf{c}_{*i} = \mathbf{A}\mathbf{b}_{*i}
$$

![[Producto de matrices.png]]
*Producto de matrices visto como producto interno fila-columna y como combinación de columnas. Slide 13.*

**Importante**: el producto de matrices **no es conmutativo** ($\mathbf{AB} \neq \mathbf{BA}$ en general).

## 6. Rango

El **rango** de una matriz es el **número máximo de filas (o columnas) linealmente independientes**. Equivalentemente, es la **dimensión de la imagen** de la transformación $f(\mathbf{x}) = \mathbf{A}\mathbf{x}$.

Para una matriz $A$ de $m \times n$:

- $\text{rank}(A) \geq 0$, con igualdad sii $A$ es la matriz nula.
- $\text{rank}(A) \leq \min(m, n)$.

Se calcula por **eliminación gaussiana** y contando filas distintas de cero.

## 7. Inversa

Si $\mathbf{A}$ es **cuadrada y de rango completo**, entonces existe una **única** matriz $\mathbf{A}^{-1}$ tal que:

$$
\mathbf{A} \mathbf{A}^{-1} = \mathbf{I}
$$

Por construcción, la fila $i$ de $\mathbf{A}$ y la columna $j$ de $\mathbf{A}^{-1}$ son:
- **Ortogonales** si $i \neq j$.
- Su producto interno vale **1** si $i = j$.

## 8. Traza

Sólo definida para **matrices cuadradas**. Es la **suma de la diagonal principal**:

$$
\text{tr}(A) = \sum_{i=1}^n a_{ii}
$$

Es un **operador lineal** con propiedades útiles:
- **Adición**: $\text{tr}(A+B) = \text{tr}(A) + \text{tr}(B)$.
- **Homogeneidad**: $\text{tr}(cA) = c \cdot \text{tr}(A)$.
- **Conmutativa en pares**: $\text{tr}(AB) = \text{tr}(BA)$, pero $\text{tr}(ABC) \neq \text{tr}(ACB)$ en general.
- **Invariante a semejanza**: $\text{tr}(P^{-1}AP) = \text{tr}(A)$.
- **Invariante a la transposición**: $\text{tr}(A) = \text{tr}(A^T)$.
- Para vectores: $\text{tr}(\mathbf{a}^T \mathbf{b}) = \text{tr}(\mathbf{a} \mathbf{b}^T)$.

## 9. Determinante

Sólo definido para **matrices cuadradas**. La inversa $\mathbf{A}^{-1}$ existe **sii $\det(\mathbf{A}) \neq 0$**.

**Para $2 \times 2$**:

$$
\det \begin{pmatrix} a_{11} & a_{12} \\ a_{21} & a_{22} \end{pmatrix} = a_{11} a_{22} - a_{12} a_{21}
$$

**Para $3 \times 3$** vale la **regla de Sarrus**:

$$
\det(A) = a_{11} a_{22} a_{33} + a_{12} a_{23} a_{31} + a_{13} a_{21} a_{32} - a_{11} a_{23} a_{32} - a_{12} a_{21} a_{33} - a_{13} a_{22} a_{31}
$$

![[Determinante - 2x2 y Sarrus.png]]
*Fórmula del determinante para 2×2 y regla de Sarrus para 3×3. Slide 18.*

**Propiedades clave** (slide 19):
- **Operaciones de fila** — intercambiar dos filas cambia el signo; multiplicar una fila por $c$ multiplica el det por $c$; sumar el múltiplo de una fila a otra **no** altera el det.
- **Transpuesta**: $\det(A^T) = \det(A)$.
- **Multiplicación**: $\det(AB) = \det(A) \det(B)$.
- **No** distribuye sobre la suma: $\det(A + B) \neq \det(A) + \det(B)$ en general.

**Aplicaciones** (slide 20):
- Calcular **autovalores** resolviendo el polinomio característico $\det(\mathbf{A} - \lambda \mathbf{I}) = 0$.
- Calcular **área** y **volumen** de paralelogramos / paralelepípedos generados por las filas (o columnas):

$$
\text{área} = |\det(A)|
$$

![[Determinante - area y volumen.png]]
*Determinante como área (paralelogramo 2D) y volumen (paralelepípedo 3D). Slide 20.*

## 10. Variantes y conexiones
- [[Matriz Definida Positiva]] — caso especial crítico para [[Filtro de Kalman]].
- [[Matriz Jacobiana]] — generaliza el gradiente, central para [[EKF]].
- [[Rotaciones]] — caso especial de matrices ortogonales.
- [[Transformaciones Homogéneas]] — extiende rotación + traslación.

## Fuentes
- `Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf`
  - slide 7 → 1. Definición
  - slides 8–9 → 2. Matriz como colección de vectores
  - slide 10 → 3. Operaciones matriciales
  - slides 11–12 → 4. Producto matriz-vector
  - slides 13–14 → 5. Producto de matrices
  - slide 15 → 6. Rango
  - slide 16 → 7. Inversa
  - slide 17 → 8. Traza
  - slides 18–20 → 9. Determinante
---
modulo: 1. Fundamentos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Fundamentos|← Fundamentos]] | [[Robotica|← Inicio]]

# Rotaciones

> Matrices que cambian la **orientación** de un vector sin alterar su norma. En robótica representan **cambios de sistema de referencia** (mundo → robot, robot → sensor, etc.).

## Prerequisitos
- [[Matrices]]
- [[Vectores]] (norma, producto interno)

## 1. Matriz ortogonal

Una matriz $Q$ es **ortogonal** sii sus vectores columna (o fila) forman una **base ortonormal**:

$$
q_{*i}^T \cdot q_{*j} = \begin{cases} 1 & \text{si } i = j \\ 0 & \text{si } i \neq j \end{cases}
$$

Como transformación lineal, una matriz ortogonal **preserva la norma** de los vectores que transforma.

**Propiedades clave**:
- La **transpuesta es la inversa**: $Q Q^T = Q^T Q = I$.
- El **determinante tiene norma unitaria** ($\pm 1$): de $1 = \det(I) = \det(Q^T Q) = \det(Q) \det(Q^T) = \det(Q)^2$.

![[Matriz ortogonal.png]]
*Definición y propiedades de las matrices ortogonales. Slide 25.*

## 2. Matriz de rotación 2D

Una **matriz de rotación** es una matriz **ortogonal con determinante $+1$** (las ortogonales con determinante $-1$ son reflexiones).

En 2D:

$$
{}^B R(\theta) = \begin{pmatrix} \cos\theta & \sin\theta \\ -\sin\theta & \cos\theta \end{pmatrix}_A
$$

donde el superíndice $B$ y subíndice $A$ indican que **transforma vectores expresados en el sistema $A$ a su expresión en el sistema $B$**, rotando el ángulo $\theta$.

![[Rotacion 2D - matriz.png]]
*Matriz de rotación 2D. Slide 26.*

![[Rotacion 2D - geometrico.png]]
*Interpretación geométrica: el sistema $B$ rotado un ángulo $\theta$ respecto del sistema $A$. Slide 27.*

**Ejemplo** — rotar el punto ${}^A\mathbf{p} = (1, 0)$ por $\theta = \pi/2$:

$$
{}^B\mathbf{p} = R\!\left(\tfrac{\pi}{2}\right) \cdot {}^A\mathbf{p} = \begin{pmatrix} 0 & 1 \\ -1 & 0 \end{pmatrix} \begin{pmatrix} 1 \\ 0 \end{pmatrix} = \begin{pmatrix} 0 \\ -1 \end{pmatrix}
$$

![[Rotacion 2D - ejemplo pi medios.png]]
*Resolución completa del ejemplo $\theta = \pi/2$. Slide 29.*

## 3. Rotaciones 3D

Para rotar alrededor de cada uno de los **ejes principales** $x$, $y$, $z$:

$$
R_x(\theta) = \begin{pmatrix} 1 & 0 & 0 \\ 0 & \cos\theta & -\sin\theta \\ 0 & \sin\theta & \cos\theta \end{pmatrix}
$$

$$
R_y(\theta) = \begin{pmatrix} \cos\theta & 0 & \sin\theta \\ 0 & 1 & 0 \\ -\sin\theta & 0 & \cos\theta \end{pmatrix}
$$

$$
R_z(\theta) = \begin{pmatrix} \cos\theta & -\sin\theta & 0 \\ \sin\theta & \cos\theta & 0 \\ 0 & 0 & 1 \end{pmatrix}
$$

![[Rotacion 3D - ejes principales.png]]
*Matrices $R_x$, $R_y$, $R_z$ para rotaciones alrededor de los ejes principales. Slide 30.*

### IMPORTANTE: las rotaciones 3D no son conmutativas

$R_x(\theta) \cdot R_y(\theta) \neq R_y(\theta) \cdot R_x(\theta)$ en general. Aplicar las mismas dos rotaciones en distinto orden produce poses diferentes.

![[Rotacion 3D - no conmutativa.png]]
*Ejemplo numérico mostrando que $R_x \cdot R_y \neq R_y \cdot R_x$ aplicado al mismo vector. Slide 31.*

Esta no conmutatividad es la razón principal por la que las [[Transformaciones Homogéneas]] son tan importantes — encapsulan el orden correctamente.

## 4. Variantes y conexiones
- Componiendo rotación + traslación se obtienen [[Transformaciones Homogéneas]].
- Caso particular de [[Matrices|matrices ortogonales]] con $\det = +1$.
- Centrales para [[2. Locomoción/Locomoción|locomoción]] (orientación del robot) y [[3. Sensores y Modelos/Sensores y Modelos|sensores]] (cambio de sistema sensor → robot).

## Fuentes
- `Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf`
  - slide 25 → 1. Matriz ortogonal
  - slides 26–29 → 2. Matriz de rotación 2D
  - slides 30–31 → 3. Rotaciones 3D
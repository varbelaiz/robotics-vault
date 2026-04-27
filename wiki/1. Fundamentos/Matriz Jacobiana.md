---
modulo: 1. Fundamentos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Fundamentos|← Fundamentos]] | [[Robotica|← Inicio]]

# Matriz Jacobiana

> Generalización del gradiente para funciones vectoriales. Codifica la **mejor aproximación lineal** de una función no lineal en un punto — pieza central del [[EKF|Filtro de Kalman Extendido]].

## Prerequisitos
- [[Matrices]]
- Derivadas parciales (cálculo)

## 1. Definición

Dada una función vectorial $f : \mathbb{R}^n \to \mathbb{R}^m$:

$$
f(\mathbf{x}) = \begin{pmatrix} f_1(\mathbf{x}) \\ f_2(\mathbf{x}) \\ \vdots \\ f_m(\mathbf{x}) \end{pmatrix}
$$

la **matriz Jacobiana** $\mathbf{F}_\mathbf{x}$ es la matriz de $m \times n$ de todas las derivadas parciales de las componentes de $f$ respecto de las componentes de $\mathbf{x}$:

$$
\mathbf{F}_\mathbf{x} = \begin{pmatrix}
\dfrac{\partial f_1}{\partial x_1} & \dfrac{\partial f_1}{\partial x_2} & \cdots & \dfrac{\partial f_1}{\partial x_n} \\
\dfrac{\partial f_2}{\partial x_1} & \dfrac{\partial f_2}{\partial x_2} & \cdots & \dfrac{\partial f_2}{\partial x_n} \\
\vdots & \vdots & & \vdots \\
\dfrac{\partial f_m}{\partial x_1} & \dfrac{\partial f_m}{\partial x_2} & \cdots & \dfrac{\partial f_m}{\partial x_n}
\end{pmatrix}
$$

![[Matriz Jacobiana - definicion.png]]
*Definición formal de la Jacobiana de una función vectorial. Slide 23.*

## 2. Interpretación

Geométricamente, la Jacobiana **es la orientación del plano tangente** a la función vectorial en un punto dado.

Es la **generalización del gradiente** de una función escalar — cuando $m = 1$ la Jacobiana se reduce a un vector fila que coincide con $\nabla f^T$.

![[Matriz Jacobiana - plano tangente.png]]
*La Jacobiana como orientación del plano tangente y como generalización del gradiente. Slide 24.*

## 3. Variantes y conexiones
- En [[EKF|Filtro de Kalman Extendido]] se usa para **linealizar** los modelos no lineales de transición $g(u, x)$ y observación $h(x)$ alrededor de la media estimada.
- La aproximación lineal habilita seguir aplicando todo el aparato del [[Filtro de Kalman]] al caso no lineal.

## Fuentes
- `Raw/Diapositivas/Teoricas/01-algebra_lineal-2.pdf`
  - slide 23 → 1. Definición
  - slide 24 → 2. Interpretación
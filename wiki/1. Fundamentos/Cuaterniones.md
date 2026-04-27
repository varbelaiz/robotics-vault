---
modulo: 1. Fundamentos
estado: completo
fuentes:
  - Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf
ultima_actualizacion: 2026-04-27
---

> [[Fundamentos|← Fundamentos]] | [[Robotica|← Inicio]]

# Cuaterniones

> Representación de rotaciones 3D con **4 números** $(q_x, q_y, q_z, q_w)$ que evita las patologías de los ángulos de Euler (gimbal lock, singularidades) y el costo de la matriz de rotación (9 números, restricciones de ortogonalidad). Es la representación **estándar en robótica de control** y la que usan ROS2/TF2.

## Prerequisitos
- [[Rotaciones]] — primero entender Euler y matriz de rotación, contra los cuales el cuaternión compite.
- [[Matrices]] — operaciones básicas, determinante, transpuesta.

## 1. ¿Por qué otra representación?

Las dos representaciones "obvias" tienen problemas serios para control de robots:

### Ángulos de Euler (Roll, Pitch, Yaw)

![[Rotaciones - Euler diagram.png]]
*Roll alrededor de x, Pitch alrededor de y, Yaw alrededor de z, slide 49 de T2.*

![[Rotaciones - Euler PRO CON.png]]
*PRO/CON: simple e intuitivo pero con gimbal lock y problemas de singularidades para control y EKF, slide 50 de T2.*

| PRO | CON |
|---|---|
| Sólo 3 parámetros | **Gimbal lock**: pierde 1 grado de libertad |
| Intuitivo | Singularidades — ecuaciones de control fallan cerca |
|  | Lo cíclico de los ángulos hay que manejarlo a mano |
|  | Dos sets distintos pueden representar la misma orientación |
|  | EKF/jacobianos sufren con la linealización |

### Matriz de rotación

![[Rotaciones - matriz formula.png]]
*Forma 2D de la matriz de rotación: $R(\theta) = [\cos\theta\;\;-\sin\theta;\;\sin\theta\;\;\cos\theta]$, slide 51 de T2. La 3D usa 9 entradas.*

![[Rotaciones - matriz PRO CON.png]]
*PRO: sin gimbal lock ni singularidades. CON: 9 números para 3 GdL, requiere mantener determinante 1 y ortogonalidad, drift numérico, slide 52 de T2.*

| PRO | CON |
|---|---|
| Sin gimbal lock | 9 números para 3 grados de libertad |
| Sin singularidades | Requiere mantener $\det(R)=1$ y $R R^\top = I$ |
| No pierde GdL | Drift numérico → pierde ortogonalidad → hay que re-ortogonalizar |
|  | Ineficiente y redundante |

**Los cuaterniones se diseñaron para ser un sweet spot**: 4 números (no 9), sin singularidades, fáciles de linealizar.

## 2. Idea geométrica

![[Cuaterniones - sphere.png]]
*La esfera unitaria $S^3 \subset \mathbb{R}^4$ que cubre las rotaciones 3D — un cuaternión es un punto en esa esfera, slide 53 de T2.*

Un cuaternión unitario $q = (q_x, q_y, q_z, q_w)$ con $\|q\| = 1$ representa una rotación 3D. La intuición:

- $(q_x, q_y, q_z)$ codifica el **eje de rotación** (escalado por $\sin(\theta/2)$).
- $q_w$ codifica el **ángulo** (vía $\cos(\theta/2)$).

Para una rotación de ángulo $\theta$ alrededor del eje unitario $\hat{u} = (u_x, u_y, u_z)$:

$$q = \left(\hat{u} \sin\tfrac{\theta}{2},\; \cos\tfrac{\theta}{2}\right) = (u_x s,\; u_y s,\; u_z s,\; \cos\tfrac{\theta}{2}), \quad s = \sin\tfrac{\theta}{2}$$

> [!info] La doble cobertura
> $q$ y $-q$ representan la **misma rotación** — la esfera $S^3$ cubre las rotaciones doblemente. Esto se ve en [el video animado](https://www.youtube.com/watch?v=d4EgbgTm0Bg).

## 3. PRO y CON

![[Cuaterniones - PRO CON.png]]
*PRO/CON de cuaterniones: estable, compacto, fácil de linealizar, buenos jacobianos. CON: poco intuitivo, requiere normalizar, slide 56 de T2 (titulado "Matriz de Rotación" en el slide pero el contenido aplica a cuaterniones).*

| PRO | CON |
|---|---|
| Sin singularidades | Poco intuitivo |
| Sin gimbal lock | Requiere normalizar para preservar $\|q\| = 1$ |
| No pierde grados de libertad |  |
| **Bueno para algoritmos de control** — error suave, jacobianos buenos |  |
| Compacto (4 números vs. 9) |  |
| Fácil de linealizar |  |
| Relación simple con velocidad angular |  |
| Estable numéricamente |  |

> [!info] Resumen comparativo
> ![[Rotaciones - 3 representaciones.png]]
> *Las tres representaciones lado a lado: Euler (intuitivo, problemático), Matriz (geométrico, redundante), Cuaternión (compacto, ideal para control), slide 48 de T2.*

## 4. Workflow típico en robótica

En la práctica, el robot **almacena y publica** orientaciones en cuaterniones (para que el control sea estable), pero el **humano programador** quiere razonar en ángulos de Euler. La solución es **convertir en la frontera**:

![[Cuaterniones - workflow control.png]]
*El robot trabaja internamente con cuaterniones; cuando hay que medir un ángulo (ej. yaw), se convierte cuaternión → euler en el conversor de la frontera, y la lógica de control corre con el ángulo escalar, slide 60 de T2.*

```
[Robot publica cuaternión] → [conversor: cuaternión → yaw] → [if yaw > 0: girar derecha; else: girar izquierda]
                                                                                ↓
                                                        [comando vuelve al robot]
```

> [!warning] El control NO debería hacer matemática directa con yaw
> Si tu controlador sólo necesita decisiones binarias (ej. "estoy mirando a la derecha?"), está bien usar el yaw escalar tras la conversión. Pero si tenés que **integrar la rotación en el tiempo** o calcular jacobianos, conviene quedarse en cuaterniones — la conversión sólo se usa al *leer* la orientación, no al actualizarla.

## 5. En ROS2 / En los TPs

- **[[ROS2 - TF2]]** publica todas las transformaciones rotacionales como cuaterniones (`geometry_msgs/Quaternion`).
- **`/odom`** del Turtlebot publica la pose con orientación en cuaternión.
- **[[TP1 - Transformaciones, Locomoción y Sensado]]** te obliga a hacer la conversión cuaternión → yaw para hacer un control en yaw.

## Conexiones
- [[Rotaciones]] — alternativas (Euler, matriz) y comparación.
- [[Transformaciones Homogéneas]] — la pose 3D combina traslación + rotación; en ROS la rotación va como cuaternión.
- [[ROS2 - TF2]] — usa cuaterniones internamente.
- [[Tutorial 2 - Speaker and Listener]] — slides 40–56 introducen cuaterniones como anticipo de TP1.
- [[TP1 - Transformaciones, Locomoción y Sensado]] — primera aplicación.

## Fuentes
- `Raw/Diapositivas/Tutoriales/Tutorial 2_ Speaker and Listener-2.pdf`
  - slides 41–48 → 1. ¿Por qué otra representación? + intro
  - slides 49–50 → 1.1 Ángulos de Euler PRO/CON
  - slides 51–52 → 1.2 Matriz de rotación PRO/CON
  - slide 53 → 2. Idea geométrica (esfera)
  - slide 56 → 3. PRO/CON de cuaterniones
  - slides 57–60 → 4. Workflow típico

---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
ultima_actualizacion: 2026-04-26
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Variables Aleatorias Continuas

> Una variable aleatoria que toma valores en un continuo, con una función de densidad de probabilidad.

## Definición  *(05-intro_robo_proba parte 1, págs. 7, 8)*

![[proba - variables aleatorias continuas.png]]
*Variable aleatoria continua con función de densidad de probabilidad.*

- $X$ toma valores en un continuo (por ejemplo, $\mathbb{R}$)
- $p(X = x)$ o $p(x)$ es una **función de densidad de probabilidad** (PDF)
- $p(x)$ no es una probabilidad en sí, sino una densidad

### Normalización  *(05-intro_robo_proba parte 1, pág. 8)*

$$\int_{-\infty}^{+\infty} p(x) \, dx = 1$$

El área total bajo la curva de densidad es 1. La probabilidad de que $X$ caiga en un intervalo $[a, b]$ es:

$$P(a \leq X \leq b) = \int_{a}^{b} p(x) \, dx$$

## Ejemplo en robótica

- Posición del robot en el plano: $(x, y) \in \mathbb{R}^2$
- Orientación: $\theta \in [0, 2\pi)$
- Distancia medida por un lidar: $d \in \mathbb{R}^+$

## Conexiones
- [[Variables Aleatorias Discretas]] — versión discreta con masa
- [[Gaussiana Multivariada]] — la distribución continua más usada en robótica
- [[Filtro de Kalman]] — trabaja con distribuciones gaussianas continuas

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf` — págs. 7, 8

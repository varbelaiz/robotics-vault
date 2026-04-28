---
modulo: 6. Mapeo
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf
  - Raw/Libro/ProbabilisticRobotics.pdf
ultima_actualizacion: 2026-04-28
---

> [[Mapeo|← Mapeo]] | [[Robotica|← Inicio]]

# Mapas de Ocupación

> Estimación probabilística de qué celdas del entorno están ocupadas, usando un filtro de Bayes binario.

## 1. Representación probabilística

Cada celda es una **variable aleatoria binaria** que modela la ocupación:

![[binary-random-variable.png]]
*Celda como variable binaria, pág. 13.*

### Suposición 2 — Independencia entre celdas

![[independent-cells.png]]
*Independencia de celdas, pág. 15.*

- Las celdas son **independientes** unas de otras
- La distribución del mapa completo es el **producto** de las distribuciones individuales:

$$P(m \mid z_{1:t}, x_{1:t}) = \prod_{i=1}^{N} P(m_i \mid z_{1:t}, x_{1:t})$$

![[map-product-distribution.png]]
*Producto de distribuciones, pág. 16–17.*

Esto permite estimar cada celda **por separado**.

> [!warning] Por qué la factorización es necesaria (Thrun et al., §9.2)
> El libro hace explícita la motivación: sin factorizar, hay que mantener una distribución sobre **todos los mapas posibles**. En un mapa de oficina típico (decenas de miles de celdas), eso significa $2^{10\,000}$ mapas distintos — astronómicamente intratable. La factorización en marginales celda-por-celda baja la dimensionalidad a algo manejable, **a costa de perder dependencias entre celdas vecinas**. Esa simplificación es la razón por la que el algoritmo de [[Mapeo con Poses Conocidas]] funciona en la práctica, pero también es su limitación principal — el libro discute en §9.4 cómo recuperar parcialmente esas dependencias con un enfoque MAP (también enriquecido en esa página).

## 2. Filtro de Bayes Binario

Para cada celda, se aplica un filtro de Bayes de estado estático:

![[binary-bayes-filter.png]]
*Filtro de Bayes binario, pág. 18.*

- **Estado estático**: la celda no cambia con el tiempo
- Se actualiza recursivamente con cada medición

## 3. Derivación a la regla recursiva

Aplicando Bayes + Markov sobre $p(m_i \mid z_{1:t}, x_{1:t})$, y haciendo lo mismo para el evento opuesto $\neg m_i$, se llega a dos expresiones que comparten denominador. Tomando el **cociente** de ambas, la mayoría de los términos (en particular el factor de normalización $p(z_t \mid z_{1:t-1}, x_{1:t})$) se cancela:

$$\frac{p(m_i \mid z_{1:t}, x_{1:t})}{p(\neg m_i \mid z_{1:t}, x_{1:t})} = \underbrace{\frac{p(m_i \mid z_t, x_t)}{1 - p(m_i \mid z_t, x_t)}}_{\text{usa } z_t} \cdot \underbrace{\frac{p(m_i \mid z_{1:t-1}, x_{1:t-1})}{1 - p(m_i \mid z_{1:t-1}, x_{1:t-1})}}_{\text{término recursivo}} \cdot \underbrace{\frac{1 - p(m_i)}{p(m_i)}}_{\text{prior}}$$

![[log-odds-ratio.png]]
*Cociente de probabilidades — los términos comunes se cancelan, slide 26.*

![[occupancy-update-rule.png]]
*Regla recursiva con tres factores: nueva medición, recursivo, prior, slides 28–29.*

### Definición de log odds

Para evitar el producto, se aplica logaritmo. El **cociente Log Odds** se define como:

$$l(x) = \log \frac{p(x)}{1 - p(x)}$$

y se puede recuperar la probabilidad invirtiéndolo:

$$p(x) = 1 - \frac{1}{1 + \exp\,l(x)}$$

![[log-odds-notation.png]]
*Definición y recuperación de $p(x)$ desde $l(x)$, slide 30.*

### Regla recursiva en log odds

Aplicando log a la regla recursiva, el **producto se transforma en suma**:

$$l(m_i \mid z_{1:t}, x_{1:t}) = \underbrace{l(m_i \mid z_t, x_t)}_{\text{inverse sensor model}} + \underbrace{l(m_i \mid z_{1:t-1}, x_{1:t-1})}_{\text{término recursivo}} - \underbrace{l(m_i)}_{\text{prior}}$$

que se reescribe como la regla iterativa:

$$l_{t,i} = \text{inv\_sensor\_model}(m_i, x_t, z_t) + l_{t-1,i} - l_0$$

![[log-odds-algorithm.png]]
*Algoritmo de mapa de ocupación en log odds — sólo sumas, slide 32.*

## 4. Algoritmo de Mapa de Ocupación

- Desarrollado a mediados de los 80' por **Moravec y Elfes**
- Originalmente para ultrasonidos ruidosos
- También llamado "**mapeo con poses conocidas**"

![[incremental-grid-update.png]]
*Ejemplo de actualización incremental, pág. 44.*

> [!info] Por qué importa un algoritmo que requiere poses conocidas (Thrun et al., §9.1)
> A primera vista la suposición "poses conocidas" es muy restrictiva — ningún robot real tiene odometría perfecta. La utilidad real del occupancy grid mapping es como **post-procesamiento de SLAM**: muchos algoritmos SLAM (incluido [[SLAM - Mapeo y Localización Simultánea|EKF-SLAM]] y FastSLAM) producen estimaciones precisas de la trayectoria $x_{1:t}$ pero mapas tipo "feature-based" o "scan-based" que no son ideales para path planning. Una vez resuelto el SLAM, se pasan las poses estimadas como si fueran conocidas y se construye un mapa de ocupación encima — el resultado tiene la geometría del SLAM con la representación volumétrica que necesita un planificador.

> [!info] Multi-Sensor Fusion: max es mejor que Bayes (Thrun et al., §9.2.1)
> Cuando hay múltiples sensores con características distintas (e.g. sonar + cámara estéreo), el approach naive es ejecutar el algoritmo Bayes con cada modalidad y combinar via filtro. **El libro recomienda lo opuesto**: construir mapas separados por tipo de sensor y combinar con el operador máximo:
>
> $$m_i = \max_k m_i^{(k)}$$
>
> Esto hace una combinación **conservadora**: una celda se considera ocupada si *cualquier* sensor la reporta ocupada. Sesga hacia "más obstáculos" — exactamente lo que queremos para path planning seguro. La razón profunda: distintos sensores detectan distintos tipos de obstáculos (cámaras ven vidrio que el sonar no detecta; el sonar ve obstáculos transparentes que la cámara no), y promediar evidencias contradictorias produce mapas inválidos donde lo que cuenta es la frecuencia de polling, no la realidad física.

## 5. Mapa de Maximum Likelihood

![[maximum-likelihood-map.png]]
*Mapa de maximum likelihood, pág. 46.*

- Se obtiene **redondeando** las probabilidades de cada celda a 0 o 1
- Produce un mapa binario ocupado/libre

## 6. Conexiones
- [[Mapas de Grilla]] — estructura base
- [[Mapeo con Poses Conocidas]] — el problema que resuelve
- [[Features vs Mapas Volumétricos]] — alternativa de representación

## Fuentes
- `Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf`
  - págs. 13–17 → 1. Representación probabilística
  - págs. 18–25 → 2. Filtro de Bayes Binario
  - págs. 26–28 → 3. Derivación a la regla recursiva *(cociente)*
  - pág. 30 → 3. Definición de log odds
  - págs. 31–32 → 3. Regla recursiva en log odds
  - págs. 33–34 → 4. Algoritmo de Mapa de Ocupación
  - pág. 46 → 5. Mapa de Maximum Likelihood
- `Raw/Libro/ProbabilisticRobotics.pdf`
  - págs. 221–223 → Por qué post-procesamiento de SLAM (§9.1)
  - págs. 224–227 → Algoritmo + dimensionalidad de la factorización (§9.2)
  - págs. 230–231 → Multi-Sensor Fusion conservativa con max (§9.2.1)
---
modulo: 6. Mapeo
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf
ultima_actualizacion: 2026-04-27
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
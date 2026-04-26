---
modulo: 6. Mapeo
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Mapeo|← Mapeo]] | [[Robotica|← Inicio]]

# Mapas de Ocupación

> Estimación probabilística de qué celdas del entorno están ocupadas, usando un filtro de Bayes binario.

## 1. Representación probabilística  *(12-mapas_de_ocupacion, págs. 13–17)*

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

## 2. Filtro de Bayes Binario  *(12-mapas_de_ocupacion, págs. 18–25)*

Para cada celda, se aplica un filtro de Bayes de estado estático:

![[binary-bayes-filter.png]]
*Filtro de Bayes binario, pág. 18.*

- **Estado estático**: la celda no cambia con el tiempo
- Se actualiza recursivamente con cada medición

## 3. Notación Log Odds  *(12-mapas_de_ocupacion, págs. 26–32)*

El cociente de probabilidades se transforma a **log odds**:

$$l = \log \frac{P(o)}{1 - P(o)}$$

![[log-odds-ratio.png]]
*Derivación log odds, pág. 26.*

### Regla recursiva en log odds

![[log-odds-notation.png]]
*Notación log odds, pág. 30.*

![[occupancy-update-rule.png]]
*Regla de actualización recursiva, pág. 29.*

- El producto se transforma en **suma** → cálculo muy eficiente
- Solo requiere sumar valores log odds por cada medición

![[log-odds-algorithm.png]]
*Algoritmo de mapa de ocupación en log odds, pág. 32.*

## 4. Algoritmo de Mapa de Ocupación  *(12-mapas_de_ocupacion, págs. 33–34)*

- Desarrollado a mediados de los 80' por **Moravec y Elfes**
- Originalmente para ultrasonidos ruidosos
- También llamado "**mapeo con poses conocidas**"

![[incremental-grid-update.png]]
*Ejemplo de actualización incremental, pág. 44.*

## 5. Mapa de Maximum Likelihood  *(12-mapas_de_ocupacion, pág. 46)*

![[maximum-likelihood-map.png]]
*Mapa de maximum likelihood, pág. 46.*

- Se obtiene **redondeando** las probabilidades de cada celda a 0 o 1
- Produce un mapa binario ocupado/libre

## 6. Conexiones
- [[Mapas de Grilla]] — estructura base
- [[Mapeo con Poses Conocidas]] — el problema que resuelve
- [[Features vs Mapas Volumétricos]] — alternativa de representación

## Fuentes
- `Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf` — págs. 1–46

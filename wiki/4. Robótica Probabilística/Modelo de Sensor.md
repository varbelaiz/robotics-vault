---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf
ultima_actualizacion: 2026-04-27
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Modelo de Sensor

> Verosimilitud $P(z_t \mid x_t, m)$: dada la pose del robot $x_t$ y el mapa $m$, qué medición $z_t$ esperamos del sensor. Es uno de los dos ingredientes del [[Filtro de Bayes]].

## Rol en el filtro de Bayes

El modelo de sensor entra en la **fase de actualización** (corrección) del filtro:

$$Bel(x_t) = \eta \, P(z_t \mid x_t, m) \, \bar{Bel}(x_t)$$

A diferencia del [[Modelo de Movimiento]], que **incrementa** la incertidumbre, las observaciones la **reducen**: cada medición pondera el belief actual por la verosimilitud de haberla obtenido en cada estado.

## Tipos de modelos de sensor

Tres familias canónicas, todas tratadas en detalle en el [[Sensores y Modelos|Módulo 3]]:

- [[Modelo de Sensor Basado en Haz]] — mezcla de cuatro componentes (hit, unexpected, max, random) para sonar y lidar. Muy fiel al sensor real pero costoso de evaluar.
- [[Modelo de Campo de Verosimilitud]] — campo precomputado de distancia al obstáculo más cercano; alternativa **eficiente y suave** al beam-based.
- [[Modelo de Detección de Landmarks]] — cuando se observan marcadores conocidos (fiduciales, features distintivas), no medición cruda.

## Causal vs. diagnóstico

El modelo de sensor da la dirección **causal**: $P(z \mid x)$ — fácil de derivar a partir de la física del sensor.

Lo que necesitamos en localización es el **diagnóstico**: $P(x \mid z)$ — dónde estoy, dada la medición. La [[Regla de Bayes]] es la herramienta que invierte esa dirección.

## Conexiones
- [[Filtro de Bayes]] — el modelo de sensor es uno de sus dos ingredientes (paso de actualización).
- [[Modelo de Movimiento]] — la otra mitad (paso de predicción).
- [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]] — los sensores cuyo modelo se construye.
- [[3. Sensores y Modelos/Sensores y Modelos|Módulo 3]] — desarrollo completo de los tres modelos.

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf`
  - slide 17 → Causal vs. diagnóstico
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf`
  - slide 31 → Rol en el filtro de Bayes
- `Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf` — fuente principal del Módulo 3.

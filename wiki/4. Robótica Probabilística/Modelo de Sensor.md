---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf
  - Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf
ultima_actualizacion: 2026-04-28
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Modelo de Sensor

> Verosimilitud $P(z_t \mid x_t, m)$: dada la pose del robot $x_t$ y el mapa $m$, qué medición $z_t$ esperamos del sensor. Es uno de los dos ingredientes del [[Filtro de Bayes]].

## 1. Rol en el filtro de Bayes

El modelo de sensor entra en la **fase de actualización** (corrección) del filtro:

$$Bel(x_t) = \eta \, P(z_t \mid x_t, m) \, \bar{Bel}(x_t)$$

A diferencia del [[Modelo de Movimiento]], que **incrementa** la incertidumbre, las observaciones la **reducen**: cada medición pondera el belief actual por la verosimilitud de haberla obtenido en cada estado.

## 2. Causal vs. diagnóstico

El modelo de sensor da la dirección **causal**: $P(z \mid x)$ — fácil de derivar a partir de la física del sensor.

Lo que necesitamos en localización es el **diagnóstico**: $P(x \mid z)$ — dónde estoy, dada la medición. La [[Regla de Bayes]] es la herramienta que invierte esa dirección.

## 3. Sensores típicos en robots móviles

Los sensores que alimentan el modelo de observación se cubren a fondo en el [[3. Sensores/Sensores|Módulo 3]]:

- **De contacto** — paragolpes.
- **Propioceptivos** — acelerómetros, giróscopos, brújulas, inclinómetros.
- **De proximidad** — sonar (tof), radar, lidar, infrarrojo.
- **Visuales** — cámaras (mono, estéreo, RGBd).
- **Basados en satélites** — GPS / GNSS.

## 4. El problema central: $P(z \mid x, m)$

Para localización con sensores de proximidad, queremos calcular la **probabilidad de la medición** $z$ dada la pose hipotética $x$ y el mapa $m$:

$$P(z \mid x, m)$$

![[Sensor proximidad - haces.png]]
*Datos típicos de sensores de proximidad: lidar (izquierda y derecha) y sonar (centro), slide 3 de 07.*

**Método**: tratar de **explicar la medición** con un modelo probabilístico de las causas físicas (reflexión correcta, obstáculos inesperados, max range, ruido aleatorio).

## 5. Familias de modelos

Hay tres modelos canónicos, según qué tipo de medición provee el sensor:

- [[Modelo de Sensor Basado en Haz]] — mezcla de cuatro componentes (hit, unexpected, max, random) para sonar y lidar. Muy fiel al sensor real pero costoso de evaluar.
- [[Modelo de Campo de Verosimilitud]] — campo precomputado de distancia al obstáculo más cercano; alternativa **eficiente y suave** al beam-based.
- [[Modelo de Detección de Landmarks]] — cuando se observan marcadores conocidos (fiduciales, features distintivas), no medición cruda.

## Conexiones
- [[Filtro de Bayes]] — el modelo de sensor es uno de sus dos ingredientes (paso de actualización).
- [[Modelo de Movimiento]] — la otra mitad (paso de predicción).
- [[Regla de Bayes]] — invierte la dirección causal a diagnóstica.
- [[3. Sensores/Sensores|Módulo 3 — Sensores]] — el hardware cuyo modelo se construye.

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf`
  - slide 17 → 2. Causal vs. diagnóstico
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf`
  - slide 31 → 1. Rol en el filtro de Bayes
- `Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf`
  - slide 2 → 3. Sensores típicos en robots móviles
  - slide 3 → 4. El problema central: $P(z \mid x, m)$

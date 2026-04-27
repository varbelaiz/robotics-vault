---
modulo: 3. Sensores y Modelos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[Sensores y Modelos|← Sensores y Modelos]] | [[Robotica|← Inicio]]

# Modelo de Campo de Verosimilitud (Likelihood Field)

> Alternativa eficiente al modelo basado en haz: en vez de mirar a lo largo del rayo, sólo se chequea el punto final contra el obstáculo más cercano.

## Prerequisitos
- [[Modelo de Sensor Basado en Haz]] — el modelo que esta página mejora.
- [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]] — sensores típicos (sonar, lidar).

## 1. Motivación: limitaciones del beam-based  *(Teóricas 07-modelos_de_sensores, slide 21)*
El modelo basado en haz tiene dos problemas:
- **Poco suave** para pequeños obstáculos y bordes — pequeñas variaciones de pose dan grandes saltos en $z_{exp}$.
- **Poco eficiente** — requiere ray-casting por haz por pose.

**Idea**: en vez de mirar a lo largo del haz, **chequear sólo el punto final** del haz contra el mapa.

## 2. Mezcla de densidades  *(Teóricas 07-modelos_de_sensores, slide 22)*
La probabilidad es una mezcla de:
- Una **distribución gaussiana** centrada en el **obstáculo más cercano** al punto final del haz.
- Una **distribución uniforme** por mediciones aleatorias.
- Una **distribución uniforme reducida** para mediciones de rango máximo.

Igual que en el beam-based, se asume independencia entre los componentes.

## 3. Ejemplo  *(Teóricas 07-modelos_de_sensores, slide 23)*
Dado un mapa $m$ con un obstáculo y dos puntos finales de haces, el campo de verosimilitud asigna probabilidad alta a regiones cerca del obstáculo:

![[Likelihood - ejemplo.png]]
*Mapa $m$ y campo de verosimilitud correspondiente; la curva $P(z \mid x, m)$ tiene picos suaves cerca de los obstáculos, slide 23.*

## 4. Aplicación a un mapa real  *(Teóricas 07-modelos_de_sensores, slide 24)*
Para el mapa del **Museo Tecnológico de San José**:

![[Likelihood - museo San Jose.png]]
*Izquierda: mapa de grilla de ocupación. Derecha: campo de verosimilitud (regiones claras = mayor probabilidad para puntos finales cerca de obstáculos), slide 24.*

El campo se pre-computa una vez y se consulta como una **tabla 2D** durante la operación — mucho más rápido que ray-casting.

## 5. Scan matching  *(Teóricas 07-modelos_de_sensores, slide 25)*
Una aplicación importante: **scan matching**. Se extrae el campo de verosimilitud de un escaneo y se compara con escaneos previos para detectar movimiento o solapamiento.

![[Likelihood - scan matching.png]]
*Extracción del campo de verosimilitud de un escaneo (puntos negros) y construcción del campo correspondiente, slide 25.*

## 6. Propiedades  *(Teóricas 07-modelos_de_sensores, slide 26)*
- **Muy eficiente**: sólo usa tablas 2D pre-computadas.
- La grilla de distancias es **suave** respecto a pequeñas variaciones en la pose del robot.
- **Permite scan matching**.
- **Ignora las propiedades físicas del haz** (no modela reflexiones especulares, ni el cono del haz, etc.).

![[Likelihood - propiedades.png]]
*Visualización del campo de verosimilitud alrededor de un obstáculo, slide 26.*

## Variantes y conexiones
- [[Modelo de Sensor Basado en Haz]] — el modelo predecesor, más fiel pero más caro.
- [[Modelo de Detección de Landmarks]] — para sensores feature-based.
- [[Mapa de Ocupación]] — el campo de verosimilitud se computa a partir del mapa de ocupación (forward-ref, M6).
- [[MCL - Filtro de Partículas]] — uso típico para localización rápida (forward-ref, M5).

## Fuentes
- `Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf` — slides 21–26.

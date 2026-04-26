---
modulo: 6. Mapeo
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Mapeo|← Mapeo]] | [[Robotica|← Inicio]]

# Features vs Mapas Volumétricos

> Dos enfoques para representar el mapa: características discretas vs grilla densa.

## 1. Comparación de enfoques  *(12-mapas_de_ocupacion, pág. 7)*

![[features-vs-volumetric.png]]
*Features vs mapas volumétricos, pág. 7.*

### Features (características)
- Puntos, líneas, esquinas identificables
- **Paramétrico**: pocos parámetros, eficiente en memoria
- Requiere **data association** (qué feature corresponde a qué)
- Usado en **EKF-SLAM**

### Mapas Volumétricos (grilla)
- Discretización densa del espacio
- **No-paramétrico**: muchos parámetros, más memoria
- No requiere data association
- Más simple, pero más costoso computacionalmente

## 2. Conexiones
- [[Mapas de Ocupación]] — enfoque volumétrico
- [[Mapas de Grilla]] — estructura de grilla
- [[EKF]] — se usa en EKF-SLAM con features

## Fuentes
- `Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf` — pág. 7

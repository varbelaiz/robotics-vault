---
modulo: 6. Mapeo
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/00-introduccion-3.pdf
  - Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Mapeo|← Mapeo]] | [[Robotica|← Inicio]]

# SLAM - Mapeo y Localización Simultánea

> Estimar simultáneamente el mapa del entorno y la posición del robot.

## 1. El problema  *(12-mapas_de_ocupacion, págs. 3–6 + programa)*

- **Mapeo con poses conocidas** es el caso simplificado donde el robot sabe exactamente dónde está
- En el caso real, el robot **no conoce su posición** — debe estimarla junto con el mapa
- Problema **acoplado**: necesitas el mapa para localizarte, y tu posición para mapear

## 2. Enfoques de SLAM
- **EKF-SLAM**: usa [[EKF]] con features como landmarks
- **Particle Filter SLAM (FastSLAM)**: usa [[MCL - Filtro de Partículas]] para la pose y filtros individuales por feature
- **Mapas de ocupación + MCL**: mapa volumétrico + filtro de partículas para localización

## 3. Conexiones
- [[Mapeo con Poses Conocidas]] — versión simplificada (poses conocidas)
- [[MCL - Filtro de Partículas]] — usado para localización dentro de SLAM
- [[EKF]] — otra aproximación (EKF-SLAM)
- [[Features vs Mapas Volumétricos]] — elección de representación afecta el enfoque

## Fuentes
- `Raw/Diapositivas/Teoricas/00-introduccion-3.pdf` — pág. 5 (programa del curso)
- `Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf` — contexto del mapeo

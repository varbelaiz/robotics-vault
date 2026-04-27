---
modulo: 6. Mapeo
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/00-introduccion-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[Mapeo|← Mapeo]] | [[Robotica|← Inicio]]

# Exploración

> Estrategias para que un robot descubra y cubra sistemáticamente un entorno desconocido.

## 1. Idea general

- El robot parte sin conocimiento del entorno
- Debe decidir hacia dónde moverse para **maximizar la información** ganada
- Estrategias comunes:
  - **Coverage path planning**: cubrir toda el área
  - **Frontier-based**: ir hacia los límites entre zona conocida y desconocida
  - **Information-theoretic**: maximizar la reducción de entropía del mapa

## 2. Conexiones
- [[SLAM - Mapeo y Localización Simultánea]] — la exploración construye el mapa mientras se localiza
- [[Mapas de Ocupación]] — el mapa guía la decisión de dónde explorar
- [[Mapeo con Poses Conocidas]] — en exploración, las poses se estiman en tiempo real

## Fuentes
- `Raw/Diapositivas/Teoricas/00-introduccion-3.pdf`
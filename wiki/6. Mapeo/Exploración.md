---
modulo: 6. Mapeo
estado: completo
fuentes: []
ultima_actualizacion: 2026-04-27
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

> [!info] Página puente
> La exploración no se cubre con un PDF dedicado en este curso — esta página resume los enfoques canónicos (frontier-based, coverage, information-theoretic) como referencia. Sin grounding directo a `Raw/`.
---
modulo: 6. Mapeo
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Mapeo|← Mapeo]] | [[Robotica|← Inicio]]

# Mapas de Grilla

> Discretización del mundo en una estructura rígida de celdas ocupadas/libres.

## 1. Concepto básico

![[grid-map-discretization.png]]
*Discretización en celdas, pág. 8.*

- Se **discretiza el mundo** en celdas de tamaño fijo
- La estructura de grilla es **rígida** — no se adapta al entorno
- Cada celda puede estar **ocupada** o **libre**
- Es un **modelo no-paramétrico**: el número de parámetros crece con la resolución
- Requiere **considerable memoria** de almacenamiento

![[mapas-motivation.png]]
*Ejemplo de mapa de grilla, pág. 9.*

## 2. Suposiciones

### Suposición 1 — Celdas binarias

![[cell-occupied-free.png]]
*Celda ocupada vs libre, pág. 12.*

- El área de una celda está **completamente libre** o **completamente ocupada**
- No hay estados intermedios

### Probabilidad de ocupación

- **Celda ocupada**: probabilidad → 1
- **Celda no ocupada**: probabilidad → 0
- **Sin información**: probabilidad = 0.5
- El entorno se asume **estático**

## 3. Conexiones
- [[Mapas de Ocupación]] — representación probabilística completa
- [[Mapeo con Poses Conocidas]] — usa grillas como estructura base

## Fuentes
- `Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf`
  - págs. 8–10 → 1. Concepto básico
  - págs. 11–14 → 2. Suposiciones
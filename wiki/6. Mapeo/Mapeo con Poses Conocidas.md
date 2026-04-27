---
modulo: 6. Mapeo
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Mapeo|← Mapeo]] | [[Robotica|← Inicio]]

# Mapeo con Poses Conocidas

> Problema simplificado: dado el trayecto exacto del robot, estimar el mapa del entorno.

## 1. El problema general del mapeo

![[mapas-motivation.png]]
*¿Cómo es el entorno?, pág. 3.*

- Formalmente, el mapa implica que, dados los datos de medición, se desea calcular el **mapa más probable**

### Con poses conocidas

![[mapas-slide-05.png]]
*Mapeo formal con poses conocidas, pág. 5–6.*

- Dadas las **mediciones** $z_{1:t}$ y las **poses** $x_{1:t}$ conocidas
- Se desea calcular: $P(m \mid z_{1:t}, x_{1:t})$

## 2. Modelo inverso de sensor — Sonar

Para actualizar las celdas se necesita un **modelo inverso de sensor**.

![[sonar-inverse-sensor-model.png]]
*Modelo inverso para sonar, pág. 35.*

### Reglas de actualización según distancia medida

![[distance-measured-vs-cell.png]]
*Valor de ocupación según distancia, pág. 37.*

- **Celda a la distancia medida $z$**: se marca como "ocupada"
- **Celdas entre el sensor y $z$**: se marcan como "libres"
- **Celdas más allá de $z + d_3$**: "sin información"

![[cell-occupied-update.png]]
*Celda ocupada al final del rango, pág. 39.*

### Dependencia con ángulo y distancia

![[angular-dependency-sonar.png]]
*Dependencia angular del modelo sonar, pág. 41.*

- Dependencia **lineal** con el ángulo de desvío del eje óptico
- Dependencia **Gaussiana** con la distancia

### Intensidad de lectura

![[mapas-slide-42.png]]
*Intensidad de lectura del sonar, pág. 42.*

![[mapas-slide-43.png]]
*Modelo resultante completo, pág. 43.*

## 3. Resultado — Mapa con Sonar

![[incremental-grid-update.png]]
*Actualización incremental de grillas, pág. 44.*

![[sonar-occupancy-map-result.png]]
*Mapa resultante con ultrasonido, pág. 45.*

## 4. Modelo para LIDAR

![[lidar-inverse-model.png]]
*Modelo inverso para LIDAR, pág. 47.*

![[lidar-occupancy-grid.png]]
*Grilla y mapa para LIDAR, pág. 48.*

### Ejemplos reales
- **MIT CSAIL, 3er piso** — mapa construido con LIDAR

![[mit-csail-map.png]]
*Mapa del MIT CSAIL, pág. 49.*

- **Universidad de Freiburg, Alemania**

![[freiburg-map.png]]
*Mapa de Freiburg, pág. 50.*

## 5. Resumen

- Los mapas de grilla son un modelo muy usado
- Las grillas de ocupación discretizan el espacio en celdas independientes
- Cada celda es una variable binaria estimada con filtro de Bayes
- El modelo log odds es rápido de calcular

## 6. Conexiones
- [[Mapas de Ocupación]] — el algoritmo de estimación
- [[Mapas de Grilla]] — la estructura de datos
- [[SLAM - Mapeo y Localización Simultánea]] — extensión a poses desconocidas

## Fuentes
- `Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf`
  - págs. 3–6 → 1. El problema general del mapeo
  - págs. 35–43 → 2. Modelo inverso de sensor — Sonar
  - págs. 44–45 → 3. Resultado — Mapa con Sonar
  - págs. 47–50 → 4. Modelo para LIDAR
  - pág. 66 → 5. Resumen
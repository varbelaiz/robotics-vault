---
modulo: 6. Mapeo
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf
  - Raw/Libro/ProbabilisticRobotics.pdf
ultima_actualizacion: 2026-04-28
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

> [!info] Aprender el modelo inverso desde datos (Thrun et al., §9.3)
> Los modelos inversos del sonar y lidar mostrados en esta página son **hardcodeados a mano** — ingeniería paciente que codifica conocimiento físico. El libro propone una alternativa: **aprenderlo con una red neuronal**. Idea:
> 1. Usar un modelo *forward* $p(z \mid x, m)$ (o un simulador físico) para generar miles de triplets $(x^{(k)}, z^{(k)}, m_i^{(k)})$.
> 2. Entrenar una función $f(x, z) \to p(\text{occ})$ por gradient descent con cross-entropy loss.
> 3. La red aprende a invertir el modelo de sensor sin que tengamos que derivarlo analíticamente.
>
> Ventajas: maneja sensores complejos (e.g. cámaras estéreo) donde el modelo inverso analítico es impracticable; aprende correlaciones espaciales (la red puede usar como input celdas vecinas, capturando dependencias que el modelo factorizado pierde); transfiere bien entre sensores del mismo tipo. La página de §6.6 del libro discute la versión análoga para landmarks.

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

> [!warning] Limitación de la factorización: conflicts en la estimación (Thrun et al., §9.4)
> El algoritmo Bayes celda-por-celda asume independencia entre celdas — pero en la realidad, una sola medición de un sensor de proximidad afecta **muchas celdas a lo largo del rayo**, induciendo dependencias. Cuando dos rayos se cruzan en una región, el algoritmo factorizado puede producir un **mapa donde la verosimilitud individual de cada celda es alta pero el mapa conjunto es contradictorio** (e.g. dos paredes que se cruzarían en el aire).
>
> La solución del libro es **MAP Occupancy Mapping** (§9.4.2): en vez de calcular el marginal por celda, buscar el mapa $m^*$ que **maximiza** $p(m \mid z, x)$ globalmente:
>
> $$m^* = \arg\max_m \log p(m \mid z, x)$$
>
> Como el espacio de mapas es enorme, se usa hill-climbing: arrancar con el mapa todo libre, "flipear" celdas individuales mientras eso aumente la log-verosimilitud. El resultado es un mapa más coherente — sin dependencias contradictorias — al costo de no representar la incertidumbre por celda. Útil cuando la salida es para path planning, no para fusión de evidencia futura.

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
- `Raw/Libro/ProbabilisticRobotics.pdf`
  - págs. 232–237 → Aprender modelo inverso con red neuronal (§9.3)
  - págs. 238–241 → Limitación de la factorización + MAP Occupancy Mapping (§9.4)
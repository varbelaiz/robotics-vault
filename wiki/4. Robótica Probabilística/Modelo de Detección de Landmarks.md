---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf
  - Raw/Libro/ProbabilisticRobotics.pdf
ultima_actualizacion: 2026-04-28
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Modelo de Detección de Landmarks

> Modelo de sensor cuando lo que se observa son **marcadores conocidos** (balizas, marcas visuales, postes), en lugar de distancias continuas. Se basa en triangulación.

## Prerequisitos
- [[Modelo de Sensor]] — el hub conceptual de $P(z \mid x, m)$.
- [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]] — sensores que pueden detectar landmarks (M3).
- [[Modelo de Sensor Basado en Haz]] o [[Modelo de Campo de Verosimilitud]] — alternativas para sensores de distancia.

## 1. Tipos de landmarks

**Otros modelos** de sensores de proximidad:
- **Macheo de mapas** (sonar, laser): generar pequeños mapas locales con los datos del sensor y machearlos contra un modelo global.
- **Características o features** (sonar, laser, visión): extraer features (puertas, pasillos, esquinas) de los datos.

**Marcadores (landmarks)**:
- **Balizas activas** — radio, GPS.
- **Marcadores pasivos** — visuales, retro-reflexivos.

El método más común es la **triangulación**.

El sensor provee:
- **distancia** al landmark, o
- **orientación** al landmark, o
- **distancia y orientación**.

> [!info] Features como reducción de dimensión (Thrun et al., §6.6.1)
> El paso de una medición cruda $z_t$ (un scan de cientos de haces, una imagen de millones de píxeles) a un vector de features $f(z_t)$ tiene una ventaja computacional enorme: **la inferencia en el espacio de features puede ser órdenes de magnitud más eficiente** que en el espacio de medición. Por eso este modelo es atractivo para localización con landmarks aun cuando los sensores subyacentes son lidars o cámaras de alta dimensión. El feature extractor $f(\cdot)$ vive fuera del modelo probabilístico — es típicamente un detector clásico (bordes, esquinas, blobs de color) o una red neuronal entrenada aparte.

## 2. Ejemplo: Sony Aibo

En la liga RoboCup con perros Sony Aibo, los **postes de colores** alrededor de la cancha funcionan como landmarks pasivos. El robot detecta su pose por triangulación con los postes visibles.

![[Landmarks - Sony Aibo.png]]
*Cancha de RoboCup con postes coloreados como landmarks; Sony Aibo, slide 29.*

## 3. Modelo probabilístico

Dado el landmark $i$ con posición conocida $(m_x(i), m_y(i))$ en el mapa, la pose actual $x = \langle x, y, \theta\rangle$ y una observación $z = \langle i, d, \alpha\rangle$ (índice del landmark, distancia medida, orientación medida), las **distancia y orientación esperadas** son:

$$\hat{d} = \sqrt{(m_x(i) - x)^2 + (m_y(i) - y)^2}$$
$$\hat{\alpha} = \text{atan2}(m_y(i) - y,\, m_x(i) - x) - \theta$$

La probabilidad de detección es el producto de dos términos independientes:

$$p_{det} = \text{prob}(\hat{d} - d, \varepsilon_d) \cdot \text{prob}(\hat{\alpha} - \alpha, \varepsilon_\alpha)$$

```
landmark_detection_model(z, x, m):
  z = ⟨i, d, α⟩, x = ⟨x, y, θ⟩
  d̂ = sqrt((m_x(i) - x)² + (m_y(i) - y)²)
  α̂ = atan2(m_y(i) - y, m_x(i) - x) - θ
  p_det = prob(d̂ - d, ε_d) · prob(α̂ - α, ε_α)
  return p_det
```

![[Landmarks - algoritmo.png]]
*Algoritmo `landmark_detection_model`, slide 30.*

> [!info] Signature y el problema de correspondencia (Thrun et al., §6.6.2–§6.6.3)
> El libro extiende el feature vector con un tercer componente además de $(r, \phi)$: una **signature** $s$ — número o vector que caracteriza el tipo de landmark (color de un poste, descriptor SIFT/ORB de una visual feature, ID de un AprilTag). Con signature, el feature observado es $\langle r, \phi, s\rangle$.
>
> Lo importante es la variable de **correspondencia** $c_t^i \in \{1, \dots, N+1\}$: a qué landmark del mapa $m$ corresponde la observación $f_t^i$. El valor $N+1$ se reserva para "ningún landmark conocido" (observación espuria).
> - **Correspondencia conocida** — $c_t^i$ se conoce de antemano (e.g. AprilTags con ID único, postes de colores distinguibles). El modelo es trivial: usar la fórmula del algoritmo directamente.
> - **Correspondencia desconocida** — $c_t^i$ es latente. Aparece el **data association problem**: hay que estimar $c_t^i$ junto con la pose, lo que reabre el espacio de hipótesis. La signature ayuda enormemente aquí — sin ella, todos los landmarks "parecen iguales" y el problema de asociación explota.
>
> Esto es central para [[SLAM - Mapeo y Localización Simultánea]]: el SLAM con correspondencia conocida es notablemente más fácil que el caso desconocido (forward-ref M6).

## 4. Distribuciones $P(z \mid x, m)$

La forma de la distribución resultante depende de **qué provee el sensor**:

- **Sólo distancia**: anillo ancho alrededor del landmark.
- **Sólo orientación**: cuña angular desde el landmark.
- **Distancia + orientación**: localización casi única — pequeño parche en una posición específica.

![[Landmarks - distribuciones.png]]
*Distintas formas de $P(z \mid x, m)$ según qué provee el sensor (sólo distancia, sólo orientación, ambas), slide 31.*

> [!info] Combinando observaciones
> Con un único landmark visto, la pose queda subdeterminada. Con tres landmarks no colineales y mediciones de distancia, la pose se determina casi unívocamente por triangulación.

> [!warning] Features pierden información (Thrun et al., §6.6.5)
> El libro hace una analogía intuitiva: cuando abrís los ojos en un lugar conocido, la imagen visual completa suele bastar para decirte exactamente dónde estás — *aun si* estabas globalmente perdido un instante antes. Si en cambio sólo "sentís" un puñado de features (la posición relativa de un poste y una ventana), tu confianza en la pose es mucho menor. Pasar al espacio de features es una **compresión con pérdida**: ganás eficiencia pero perdés la riqueza informacional del scan crudo. Por eso, en hardware moderno, los algoritmos state-of-the-art tienden a usar mediciones densas (likelihood field, beam) en lugar de features. Las features siguen siendo importantes en escenarios con compute limitado, sensores muy ruidosos, o problemas de correspondencia complejos como [[SLAM - Mapeo y Localización Simultánea]].

## Variantes y conexiones
- [[Modelo de Sensor Basado en Haz]] — alternativa para sensores de distancia continua.
- [[Modelo de Campo de Verosimilitud]] — alternativa eficiente para lidar/sonar.
- [[MCL - Filtro de Partículas]] — usa este modelo cuando hay landmarks (forward-ref, M5).
- [[EKF]] — feature-based EKF SLAM consume landmarks (forward-ref, M5).

## Fuentes
- `Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf`
  - slides 27–28 → 1. Tipos de landmarks
  - slide 29 → 2. Ejemplo: Sony Aibo
  - slide 30 → 3. Modelo probabilístico
  - slide 31 → 4. Distribuciones $P(z \mid x, m)$
- `Raw/Libro/ProbabilisticRobotics.pdf`
  - pág. 147 → Features como reducción de dimensión (§6.6.1)
  - págs. 148–149 → Signature + correspondence problem (§6.6.2–§6.6.3)
  - págs. 152–153 → Tradeoff features vs raw — analogía visual (§6.6.5)
# Wiki de Robótica

> Knowledge base de la materia universitaria de **Robótica** (estilo *LLM Wiki*).
> Schema operativo: [[CLAUDE]]. Diseño y rationale: [[DESIGN]].

---

## Módulos

| #   | Módulo                                                                          | Estado      | Páginas completas | Total |
| --- | ------------------------------------------------------------------------------- | ----------- | ----------------- | ----- |
| 1   | [[1. Fundamentos/Fundamentos\|Fundamentos]]                                     | Completo    | 8                 | 8     |
| 2   | [[2. Locomoción/Locomoción\|Locomoción]]                                        | Completo    | 5                 | 5     |
| 3   | [[3. Sensores y Modelos/Sensores y Modelos\|Sensores y Modelos]]                | Completo    | 5                 | 5     |
| 4   | [[4. Robótica Probabilística/Robótica Probabilística\|Robótica Probabilística]] | Completo    | 11                | 11    |
| 5   | [[5. Filtros Bayesianos/Filtros Bayesianos\|Filtros Bayesianos]]                | En progreso | 9                 | 9     |
| 6   | [[6. Mapeo/Mapeo\|Mapeo]]                                                       | Completo    | 6                 | 6     |
| 7   | [[7. ROS2 y TPs/ROS2 y TPs\|ROS2 y TPs]]                                        | Esqueleto   | 0                 | 21    |

> Estados posibles: `Esqueleto` (sin contenido grounded) · `En progreso` (alguna ingesta hecha) · `Completo` (todas las fuentes del módulo ingeridas).

---

## Catálogo

> Lista plana de todas las páginas del wiki, agrupada por módulo.
> Actualizada en cada ingesta. Una línea por página: `[[link]] — descripción corta.`

- [[log|Bitácora del wiki]]

## Fuentes en `Raw/`
- `Raw/Diapositivas/Teoricas/` — 14 PDFs teóricos (00 → 12)
- `Raw/Diapositivas/Tutoriales/` — 8 tutoriales ROS2 (T1 → T8)
- `Raw/TPs/` — Enunciados TP1, TP2, TP3

## 1. Fundamentos — Completo ✅
- [[Fundamentos|← Fundamentos]] — Mapa del módulo: conceptos introductorios, álgebra lineal, rotaciones, transformaciones.
- [[Introducción a la Robótica Móvil]] — Panorama del campo: navegación, percepción, localización, mapeo, exploración.
- [[Paradigmas de Control de Robots]] — Evolución de enfoques: clásico jerárquico, reactivo, híbrido.
- [[Vectores]] — Arreglos ordenados de números; producto interno, dependencia lineal.
- [[Matrices]] — Operaciones, productos, rango, inversa, traza, determinante.
- [[Matriz Definida Positiva]] — Análoga al "número positivo"; covarianzas en filtros.
- [[Matriz Jacobiana]] — Generalización del gradiente; linealización en EKF.
- [[Rotaciones]] — Matrices ortogonales; rotaciones 2D y 3D, no conmutatividad.
- [[Transformaciones Homogéneas]] — Rototraslación; pose del robot; composición.

## 2. Locomoción — Completo ✅
- [[Locomoción|← Locomoción]] — Mapa del módulo: cinemática, accionamientos, odometría y modelos de movimiento.
- [[Locomoción y Tipos de Accionamientos]] — Tipos de locomoción, ruedas, accionamientos canónicos y restricciones holonómicas.
- [[Cinemática del Robot Diferencial]] — Pose 2D, derivación completa de la cinemática directa con ICC.
- [[Otros Accionamientos - Ackermann, Síncrono, Omni]] — Ackermann, síncrono, mecanum, vehículos especiales.
- [[Odometría y Modelo de Movimiento (Odometría)]] — Dead reckoning + modelo probabilístico basado en encoders.
- [[Modelo de Movimiento (Velocidad)]] — Modelo probabilístico cuando el control viene como $(v, \omega)$.

## 3. Sensores y Modelos — Completo ✅
- [[Sensores y Modelos|← Sensores y Modelos]] — Mapa del módulo: hardware sensorial + modelos probabilísticos $p(z\|x,m)$.
- [[Sensores Internos]] — Clasificación, encoders, táctiles, magnetómetro, giróscopo, acelerómetro, IMU.
- [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]] — GPS/RTK, sonar, lidar, RGBd, estéreo.
- [[Modelo de Sensor Basado en Haz]] — Mezcla de hit/unexp/max/rand para sonar y lidar.
- [[Modelo de Campo de Verosimilitud]] — Alternativa eficiente y suave al beam-based.
- [[Modelo de Detección de Landmarks]] — Modelo cuando se observan marcadores conocidos.

## 4. Robótica Probabilística — Completo ✅
- [[Robótica Probabilística|← Robótica Probabilística]] — Axiomas, variables aleatorias, Bayes, Markov, y el filtro de Bayes como base para localización.
- [[Introducción a la Robótica Probabilística]] — Idea general de representar incertidumbre explícitamente en robótica.
- [[Axiomas de Probabilidad]] — Fundamentos formales de la teoría de probabilidad.
- [[Variables Aleatorias Discretas]] — Espacios de estados discretos y distribuciones.
- [[Variables Aleatorias Continuas]] — Densidades de probabilidad en espacios continuos.
- [[Probabilidad Conjunta]] — Distribuciones multivariadas y marginalización.
- [[Probabilidad Condicional]] — Condicionamiento, independencia, y razonamiento causal vs. diagnóstico.
- [[Regla de Bayes]] — Actualización de creencias con evidencia + actualización recursiva con múltiples mediciones.
- [[Suposición de Markov]] — Dependencia temporal local.
- [[Filtro de Bayes]] — Algoritmo recursivo de estimación: predict y update.
- [[Modelo de Movimiento]] — Transición probabilística $P(x_t \mid u_t, x_{t-1})$ con ejemplo de FSM "cerrar puerta".
- [[Modelo de Sensor]] — Hub de los modelos de observación $P(z_t \mid x_t, m)$ del Módulo 3.

## 5. Filtros Bayesianos — En progreso (9/9)
- [[Filtros Bayesianos|← Filtros Bayesianos]] — Implementaciones del filtro de Bayes: discretos, Kalman, y partículas.
- [[Filtros Discretos]] — Representación en grilla; O(n) con convolución.
- [[MCL - Filtro de Partículas]] — Localización Monte Carlo con partículas.
- [[Muestreo por Importancia]] — Técnica de muestreo para MCL.
- [[Gaussiana Multivariada]] — Distribución para filtros de Kalman.
- [[Filtro de Kalman]] — Estimador óptimo para sistemas lineales gaussianos.
- [[EKF]] — Linealización con Jacobianos.
- [[UKF]] — Sigma points sin Jacobianos.
- [[Puntos Sigma]] — Construcción del conjunto sigma para UKF.
- [[Descomposición de Cholesky]] — Raíz cuadrada de matriz para UKF.

## 6. Mapeo — Completo ✅
- [[Mapeo|← Mapeo]] — Representación del entorno: mapas de ocupación, SLAM, exploración.
- [[Mapas de Ocupación]] — Grilla probabilística celda por celda.
- [[Mapas de Grilla]] — Estructura rígida de celdas ocupadas/libres.
- [[Mapeo con Poses Conocidas]] — Problema simplificado de mapeo.
- [[SLAM - Mapeo y Localización Simultánea]] — Problema completo de SLAM.
- [[Features vs Mapas Volumétricos]] — Dos enfoques de representación.
- [[Exploración]] — Descubrimiento de entornos desconocidos.

## 7. ROS2 y TPs — Esqueleto (0/21)
- [[ROS2 y TPs|← ROS2 y TPs]] — Framework ROS2, simulación, tutoriales, y trabajos prácticos.
- [[ROS2 - Conceptos Base]] — Introducción al framework: nodos, topics, mensajes, servicios.
- [[ROS2 - Nodos]] — Unidad básica de ejecución en ROS2.
- [[ROS2 - Topics]] — Canales de comunicación pub/sub asíncrona.
- [[ROS2 - Mensajes]] — Tipos de datos estructurados en ROS2.
- [[ROS2 - Publisher]] — Lado emisor de la comunicación.
- [[ROS2 - Subscriber]] — Lado receptor de la comunicación.
- [[ROS2 - TF2]] — Transformaciones de coordenadas entre frames.
- [[ROS2 - Launch Files]] — Archivos para arranque múltiple de nodos.
- [[Gazebo y rviz]] — Simulación física y visualización 3D.
- [[Tutorial 1 - Bienvenida e Instalación de ROS2]] — Instalación y primeros pasos en ROS2.
- [[Tutorial 2 - Speaker and Listener]] — Pub/sub básico en Python.
- [[Tutorial 3 - Interactuando con Gazebo]] — Simulación, TF, y RViz2.
- [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — Visualización y preparación para TPs.
- [[Tutorial 5 - Bayes]] — Ejercicios de probabilidad y Bayes.
- [[Tutorial 6 - Movimiento, Sensores e Histogramas]] — Modelos probabilísticos en ROS2.
- [[Tutorial 7 - Filtro de Partículas]] — MCL en ROS2.
- [[Tutorial 8 - KF, EKF, UKF]] — Filtros de Kalman en ROS2.
- [[TP1 - Transformaciones, Locomoción y Sensado]] — Transformaciones + datos reales de odometría.
- [[TP2 - Modelos Probabilísticos y Filtros Discretos]] — Muestreo + filtro de histograma.
- [[TP3 - Filtros de Partículas]] — MCL completo en ROS2.

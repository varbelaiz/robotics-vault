# Wiki de Robótica

> Knowledge base de la materia universitaria de **Robótica** (estilo *LLM Wiki*).
> Schema operativo: [[CLAUDE]]. Diseño y rationale: [[DESIGN]].

---

## Módulos

| #   | Módulo                                                                          | Estado      | Páginas completas | Total |
| --- | ------------------------------------------------------------------------------- | ----------- | ----------------- | ----- |
| 1   | [[1. Fundamentos/Fundamentos\|Fundamentos]]                                     | Completo    | 9                 | 9     |
| 2   | [[2. Locomoción/Locomoción\|Locomoción]]                                        | Completo    | 5                 | 5     |
| 3   | [[3. Sensores y Modelos/Sensores y Modelos\|Sensores y Modelos]]                | Completo    | 5                 | 5     |
| 4   | [[4. Robótica Probabilística/Robótica Probabilística\|Robótica Probabilística]] | Completo    | 11                | 11    |
| 5   | [[5. Filtros Bayesianos/Filtros Bayesianos\|Filtros Bayesianos]]                | Completo    | 9                 | 9     |
| 6   | [[6. Mapeo/Mapeo\|Mapeo]]                                                       | Completo    | 6                 | 6     |
| 7   | [[7. ROS2/ROS2\|ROS2]]                                                          | Completo    | 18                | 18    |
| 8   | [[8. TPs/TPs\|TPs]]                                                             | Completo    | 4                 | 4     |

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
- [[Rotaciones]] — Matrices ortogonales; rotaciones 2D y 3D, no conmutatividad, comparación de representaciones.
- [[Cuaterniones]] — Representación de rotaciones 3D con 4 números; sin gimbal lock; estándar en robótica de control.
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

## 7. ROS2 — Completo ✅
- [[ROS2|← ROS2]] — Framework ROS2 + 8 tutoriales que aterrizan los modelos teóricos en código.
- [[ROS2 - Conceptos Base]] — Middleware distribuido: nodos, topics, mensajes; arquitectura del stack del curso (Humble + Gazebo + Turtlebot3).
- [[ROS2 - Nodos]] — Unidad de ejecución; patrón timer + callback en vez de `while True` para no bloquear el grafo.
- [[ROS2 - Topics]] — Canal pub/sub asíncrono y tipado; multiplicidad libre, descubrimiento DDS.
- [[ROS2 - Mensajes]] — Tipos estructurados; composición jerárquica (Odometry → Header + Pose + Twist; Pose → Point + Quaternion).
- [[ROS2 - Publisher]] — Lado emisor; `create_publisher` + timer con callback.
- [[ROS2 - Subscriber]] — Lado receptor reactivo; `create_subscription` + callback de evento.
- [[ROS2 - TF2]] — Frames, transformaciones dinámicas vs. estáticas, árbol con `view_frames`, API `Buffer + TransformListener`.
- [[ROS2 - Launch Files]] — Orquestar nodos con `generate_launch_description()` + `Node(...)`; package.xml + setup.py data_files.
- [[Gazebo y rviz]] — Gazebo (simulador físico, GPU) + RVIZ (visor 3D con displays, views, frames, status).
- [[Tutorial 1 - Bienvenida e Instalación de ROS2]] — Instalación de Humble + Gazebo + Turtlebot3 (Robostack y ROS2 crudo).
- [[Tutorial 2 - Speaker and Listener]] — Pub/sub básico + workspace + paquete + anticipo de TP1 (rotaciones 3D).
- [[Tutorial 3 - Interactuando con Gazebo]] — Lanzar simulación, rqt_graph, TF2, comandos topic, teleop + Commander, recorder offline.
- [[Tutorial 4 - Mensajes, rviz, Launch y TP1_TP2]] — RVIZ a fondo, composición de mensajes, escribir launch files, preview de TP1 y TP2.
- [[Tutorial 5 - Bayes]] — Ejercicio canónico del sensor defectuoso (Bayes recursivo de N=1 a N=10, código + plot), fusión de sensores y rederivación del filtro.
- [[Tutorial 6 - Movimiento, Sensores e Histogramas]] — `sample_motion_model_odometry` con ruido, modelo de sensor basado en haz + Bresenham, likelihood field eficiente, filtro discreto (preview TP2).
- [[Tutorial 7 - Filtro de Partículas]] — MCL paso a paso (predicción + ponderación con landmarks + remuestreo Stochastic Universal Sampling), features y data association.
- [[Tutorial 8 - KF, EKF, UKF]] — Trick numérico log-likelihoods, derivación KF predict/correct, EKF con Jacobianos + caso de robot 2D donde la linealización falla, UKF con sigma points, TP4 paralelo.

## 8. TPs — Completo ✅
- [[TPs|← TPs]] — Trabajos prácticos del curso. Cada TP integra varios módulos teóricos con código ROS2.
- [[TP1 - Transformaciones, Locomoción y Sensado]] — Transformaciones 2D, datos reales (odom.csv + scan.pkl con cuaternión + LIDAR), `diffdrive` con secuencia de comandos.
- [[TP2 - Modelos Probabilísticos y Filtros Discretos]] — 3 muestreadores de gaussiana (suma de 12, rechazo, Box-Muller), modelo de odometría con ruido + nodo ROS2 visualizado en RVIZ, filtro discreto sobre mundo 1D de 20 celdas.
- [[TP3 - Filtros de Partículas]] — MCL completo en ROS2: campo de verosimilitud desde OccupancyGrid, sample_motion_model por partícula, update_particles con scan + grid, resampleo con deepcopy, log-likelihood trick.

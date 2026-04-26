---
modulo: 3. Sensores y Modelos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/04-sensores-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[_Overview|← Sensores y Modelos]] | [[Home|← Inicio]]

# Sensores Externos: GNSS, Ultrasonido, Lidar, Cámaras

> Sensores exteroceptivos que adquieren información del entorno: posición global por satélite, distancia por tiempo de vuelo (sonar, lidar), información visual (RGBd, estéreo).

## Prerequisitos
- [[Sensores Internos]] — la otra mitad de la familia de sensores y la clasificación general.

## 1. GNSS (Global Navigation Satellite System)  *(Teóricas 04-sensores, slide 10)*
Sistema basado en satélites para estimar la pose **global** por triangulación. Se toma la distancia a **cuatro satélites** (uno por cada incógnita: latitud, longitud, altitud y offset en tiempo del reloj del receptor).

La distancia a cada satélite se obtiene midiendo el tiempo que tarda en llegar la señal de radio.

Sistemas existentes:
- **GPS** (USA): 6 planos orbitales, 24 satélites + spare, 55° inclinación, ~20.200 km.
- **Galileo** (Europa): 3 planos, 27 satélites + spare, 56°, ~23.616 km.
- **GLONASS** (Rusia): 3 planos, 21 satélites + spare, 64.8°, ~19.100 km.

![[GNSS - GPS.png]]
*Triangulación a partir de satélites GPS, slide 10.*

## 2. GNSS-RTK (Real-Time Kinematics)  *(Teóricas 04-sensores, slide 11)*
Variante de alta precisión:
- Se requieren **al menos dos receptores GPS**: estación base + unidad móvil.
- La estación base retransmite la **fase de la onda portadora** de la señal del satélite.
- La unidad móvil compara su medición de fase con la fase recibida de la base (resolución de ambigüedad).

Permite precisión centimétrica.

![[GNSS - RTK.png]]
*Sistema GNSS-RTK con estación base y unidad móvil, slide 11.*

## 3. Sensores de ultrasonido (sonar)  *(Teóricas 04-sensores, slides 12–13)*
Sensores activos que:
1. Emiten una **señal de ultrasonido**.
2. Esperan hasta recibir el eco.
3. Miden el **tiempo de vuelo** (ToF).

La distancia se calcula como:
$$d = \frac{c\,t}{2}$$

donde $c$ es la velocidad del sonido:
$$c = \sqrt{\gamma R T}$$

con $\gamma$ ratio de calores específicos, $R$ la constante del gas, $T$ la temperatura en Kelvin. En aire a presión estándar y 20° C, $c \approx 343$ m/s.

![[Ultrasonido - sensores.png]]
*Sensores HC-SR04 (Arduino) y Polaroid 6500 (B21), slide 12.*

## 4. Propiedades y errores del ultrasonido  *(Teóricas 04-sensores, slides 14–17)*
**Perfil de la señal**: el haz no es un rayo, sino un cono con lóbulos secundarios:

![[Ultrasonido - perfil polar.png]]
*Perfil polar del lóbulo principal y secundarios, slide 14.*

**Fuentes de error**:
- **Ángulo de apertura**: el haz amplio puede detectar objetos fuera del eje.
- **Crosstalk**: si dos sensores activos cercanos se interfieren.
- **Reflexión especular**: el haz rebota en una superficie inclinada y no vuelve.

![[Ultrasonido - errores.png]]
*Tres fuentes de error: ángulo de apertura, crosstalk, reflexión especular, slide 15.*

**Medición típica** en una habitación: el patrón muestra fuertes desviaciones por las propiedades del haz:

![[Ultrasonido - medicion tipica.png]]
*Medición típica de ultrasonido en una habitación, slide 16.*

**Operación paralela**: con apertura de 15°, se necesitan 24 sensores para cubrir 360°. Para un alcance de 10 m, el ToF es ~0.06 s; un escaneo completo serial requiere $24 \times 0.06 \approx 1.45$ s. Para mayor tasa hay que activar en paralelo, con riesgo de **crosstalk**.

## 5. Sensor de triangulación óptica  *(Teóricas 04-sensores, slide 18)*
Un haz láser colimado golpea el objetivo; el haz reflejado entra a un PSD (Position-Sensitive Device) o cámara lineal. La distancia se calcula por triangulación geométrica:

$$D = f\,\frac{L}{x}$$

donde $L$ es la línea base, $f$ la distancia focal y $x$ la posición del haz reflejado en el PSD.

![[Triangulacion optica.png]]
*Geometría de triangulación óptica con láser, lente y PSD, slide 18.*

## 6. Escáner láser (LiDAR)  *(Teóricas 04-sensores, slides 19–22)*
**LiDAR (Light Detection and Ranging)** o **Laser Rangefinder**:
- Emite pulsos de luz; la distancia se calcula por tiempo de vuelo del haz reflejado.
- Un **espejo rotatorio** dirige el haz para barrer un FOV (típicamente 180° o 360°).

![[Lidar - tipos.png]]
*SICK LMS, principio del espejo rotatorio, modelos Velodyne / Hokuyo, slide 19.*

**Propiedades**:
- Alta precisión.
- Distancia máxima: ~120 m (SICK LMS), ~6 m (Hokuyo).
- Amplio FOV.
- 2D o 3D.

![[Lidar - 3D.png]]
*Nube de puntos 3D de un LiDAR multi-haz, slide 21.*

**Lectura**: los datos son un array de distancias `[1, 1.2, 1.5, 0.1, 81.9, ...]`. Asumiendo FOV de 180°, el primer haz empieza en $-90°$.

![[Lidar - lectura array.png]]
*Cómo se interpreta el array de lecturas del LiDAR, slide 22.*

## 7. Cámara de luz estructurada (RGBd / Kinect)  *(Teóricas 04-sensores, slide 25)*
Proyecta un **patrón conocido de píxeles** (rejillas o barras) sobre la escena. La deformación del patrón al golpear superficies a distintas profundidades permite calcular la distancia de cada píxel.

![[Camara - RGBd Kinect.png]]
*Cámara estructurada Kinect: proyecta patrón, captura deformación, reconstruye 3D, slide 25.*

## 8. Cámara estéreo  *(Teóricas 04-sensores, slide 27)*
Calcula profundidad a través de **dos imágenes con perspectivas diferentes** (línea base $b$). El desplazamiento de un píxel entre ambas imágenes (disparidad) es inversamente proporcional a la profundidad.

![[Camara - estereo.png]]
*Intel RealSense D457: imagen estéreo izquierda + mapa de profundidad reconstruido, slide 27.*

## Variantes y conexiones
- [[Modelo de Sensor Basado en Haz]] — modelo probabilístico canónico para lidar/sonar.
- [[Modelo de Campo de Verosimilitud]] — alternativa más eficiente.
- [[Sensores Internos]] — la otra mitad.

## Fuentes
- `Raw/Diapositivas/Teoricas/04-sensores-3.pdf` — slides 10–27.

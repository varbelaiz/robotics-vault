---
modulo: 3. Sensores y Modelos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/04-sensores-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[_Overview|← Sensores y Modelos]] | [[Home|← Inicio]]

# Sensores Internos

> Sensores que miden valores internos del sistema: encoders, sensores de contacto, IMU. La base para odometría y para conocer el estado del robot sin información del entorno.

## Prerequisitos
- *(ninguno — entrada al módulo)*

## 1. Clasificación de sensores  *(Teóricas 04-sensores, slides 2–3)*

Los sensores se clasifican en dos ejes ortogonales:

**Por la fuente de energía** (cómo perciben el entorno):
- **Activos** — emiten energía y miden la respuesta. Ej: ultrasonido, lidar, infrarrojo, radar (basados en tiempo de vuelo o corrimiento de fase).
- **Pasivos** — reciben energía del entorno. Ej: cámaras, sensores táctiles (basados en intensidad).

![[Sensores - clasificacion percepcion.png]]
*Sensores activos vs pasivos para percepción del entorno, slide 2.*

**Por el tipo de percepción**:
- **Propioceptivos** (introceptivos) — miden valores **internos del sistema**: velocidad del motor, ángulos de las ruedas, ángulos del brazo, voltaje de batería.
- **Exteroceptivos** — adquieren **información del entorno**: distancias, intensidad de luz, amplitud de sonido.

> [!info] Esta página vs la siguiente
> Esta página cubre los sensores **propioceptivos** (encoders, IMU) más los **táctiles** (que técnicamente son exteroceptivos pero pasivos y muy locales). La siguiente cubre los exteroceptivos a distancia.

## 2. Encoders ópticos  *(Teóricas 04-sensores, slide 4)*
Sensores propioceptivos que miden el giro del rotor por la cantidad de luz que pasa entre un LED y un fotodetector:

- **Encoder incremental** — disco con franjas alternadas; salida pulsada A y B desfasadas para detectar dirección. Cuenta pulsos para medir desplazamiento.
- **Encoder absoluto** — disco con patrón único por posición (e.g. código Gray); salida indica la posición absoluta sin necesidad de calibración inicial.

![[Encoders opticos.png]]
*Encoder incremental (A, B, dirección CW) y encoder absoluto (A, B, C), slide 4.*

> [!info] Conexión con M2
> Los encoders son la base de la [[Odometría y Modelo de Movimiento (Odometría)|odometría]]: sus lecturas alimentan las ecuaciones de cinemática inversa para estimar el desplazamiento del robot.

## 3. Sensores táctiles  *(Teóricas 04-sensores, slide 5)*
Miden el **contacto físico** con objetos:

- **Sensor de contacto** — interruptor que se cierra al tocar.
- **Sensor de paragolpe (bumper)** — par de microswitches montados sobre un brazo con resorte. El bumper rodea al robot y detecta colisiones desde cualquier dirección.

![[Sensores tactiles.png]]
*Sensor de contacto y bumper con resortes y microswitches, slide 5.*

## 4. Magnetómetro  *(Teóricas 04-sensores, slide 6)*
Mide la intensidad del **campo magnético** mediante el efecto Hall: produce un voltaje proporcional a la intensidad. Tres sensores ortogonales forman un **magnetómetro triaxial** que entrega el vector de campo.

Aplicación típica: brújula digital usando el campo magnético terrestre.

![[Magnetometro.png]]
*Líneas de campo magnético terrestre, sonda Pioneer, sensor Hall, slide 6.*

## 5. Giróscopo  *(Teóricas 04-sensores, slide 7)*
Mide **velocidades angulares**:
- Giróscopo mecánico clásico: la magnitud de la precesión es proporcional a la fuerza aplicada e inversamente proporcional a la velocidad de rotación (RPM).
- Giróscopo MEMS: usa la **fuerza de Coriolis** — fuerza tangencial experimentada por un objeto giratorio en movimiento radial.

![[Giroscopo.png]]
*Giróscopo mecánico y principio MEMS basado en Coriolis, slide 7.*

## 6. Acelerómetro  *(Teóricas 04-sensores, slide 8)*
Mide aceleración midiendo el cambio en la **capacitancia**:
- Una masa unida a un resorte se mueve en una dirección.
- Las placas exteriores fijas forman un capacitor con la masa.
- Cuando se aplica aceleración, la masa se mueve y la capacitancia varía.

![[Acelerometro.png]]
*Acelerómetro MEMS: masa, resortes, placas fijas formando capacitores C1, C2, slide 8.*

## 7. IMU (Unidad de Medición Inercial)  *(Teóricas 04-sensores, slide 9)*
Una **IMU** combina típicamente:
- 3 giróscopos ortogonales.
- 3 acelerómetros ortogonales.

Permite estimar:
- **Orientación**: integrando giróscopo en el tiempo (yaw, pitch, roll).
- **Velocidad lineal**: integrando acelerómetro.
- **Posición**: integrando velocidad (es decir, doble integración del acelerómetro).

![[IMU.png]]
*IMU con sus ejes Yaw (z), Pitch (y), Roll (x), slide 9.*

> [!warning] Doble integración → error cuadrático
> Como la posición se obtiene integrando dos veces el acelerómetro, cualquier **error residual del sensor da error cuadrático en posición**. Por eso la IMU sola no sirve para localización; debe fusionarse con sensores que provean referencias absolutas (GPS, lidar, etc.).

## 8. Tabla resumen  *(Teóricas 04-sensores, slide 28)*
Clasificación completa: PC = propioceptivo, EC = exteroceptivo, A = activo, P = pasivo.

![[Sensores - tabla resumen.png]]
*Tabla resumen de sensores con clasificación PC/EC y A/P, slide 28.*

## Variantes y conexiones
- [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]] — los exteroceptivos a distancia.
- [[Odometría y Modelo de Movimiento (Odometría)]] — consume las lecturas de encoders.
- [[Modelo de Movimiento (Velocidad)]] — útil cuando se sustituyen encoders por IMU.

## Fuentes
- `Raw/Diapositivas/Teoricas/04-sensores-3.pdf` — slides 2–9, 28.

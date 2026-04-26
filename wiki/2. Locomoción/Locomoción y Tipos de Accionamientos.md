---
modulo: 2. Locomoción
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/03-locomocion-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[_Overview|← Locomoción]] | [[Home|← Inicio]]

# Locomoción y Tipos de Accionamientos

> Cómo se mueve un robot: tipos de locomoción, tipos de ruedas, accionamientos canónicos y restricciones cinemáticas.

## Prerequisitos
- *(ninguno — esta es la página de entrada al módulo)*

## 1. Tipos de locomoción  *(Teóricas 03-locomocion, slide 2)*
La locomoción en robótica se refiere al movimiento y desplazamiento de robots. Los tipos principales son:
- **Terrestre**
- **Acuática**
- **Anfibia**
- **Aérea**
- **Espacial**

![[Locomocion - tipos.png]]
*Ejemplos de cada tipo: Mars rover (terrestre), USV (acuática), cuadrúpedo (terrestre con patas), ASIMO (bípedo), slide 2.*

## 2. Locomoción terrestre  *(Teóricas 03-locomocion, slides 3–4)*
Dentro de la locomoción terrestre, las dos grandes familias son **patas** y **ruedas**.

![[Locomocion - terrestre.png]]
*Configuraciones de patas: mamíferos (2 o 4), reptiles (4), insectos (6), slide 3.*

El tipo de locomoción depende de:
- El **tipo de entorno** de operación.
- Los **objetivos de la misión**.
- Las **limitaciones de diseño**.

|  | **Patas** | **Ruedas** |
|---|---|---|
| Adaptación al terreno | Robusto a tipo de terreno | Poco eficiente en terrenos blandos |
| Grados de libertad (DOF) | Mayor cantidad | Menor cantidad |
| Control | Mayor fuerza de control | Menor fuerza de control |
| Eficiencia energética | Baja | Mayor |

![[Locomocion - patas vs ruedas.png]]
*Comparación de eficiencia (potencia específica vs velocidad), slide 4.*

## 3. Tipos de accionamiento  *(Teóricas 03-locomocion, slide 6)*
Los seis accionamientos canónicos para robots con ruedas:

- **Diferencial** — dos ruedas motrices independientes + una rueda de apoyo (castor).
- **Skid-steer** — cuatro ruedas, control diferencial por lado (estilo tanque).
- **Triciclo** — una rueda directriz adelante + dos ruedas pasivas atrás.
- **Car-like (Ackermann)** — dirección tipo automóvil.
- **Omni-wheel** — ruedas omnidireccionales (típicamente 3 a 120°).
- **Mecanum** — variante de omnidireccional con rodillos a 45° en cada rueda.

![[Accionamientos - tipos.png]]
*Los seis accionamientos típicos, slide 6.*

## 4. Restricciones holonómicas y no-holonómicas  *(Teóricas 03-locomocion, slides 7–8)*
Una **restricción no-holonómica** limita los **movimientos incrementales** posibles dentro del espacio de configuración del robot, sin reducir el espacio de configuración en sí.

Los robots con accionamiento **diferencial** o **Ackermann** se mueven en trayectorias circulares y **no pueden moverse lateralmente** — son no-holonómicos.

Los robots con ruedas **omni** o **mecanum** sí pueden moverse lateralmente — son holonómicos (no tienen restricciones no-holonómicas en el plano).

![[Holonomica - no-holonomicas.png]]
*Robots no-holonómicos típicos vs robots holonómicos con omni/mecanum, slide 7.*

| | **No-holonómica** | **Holonómica** |
|---|---|---|
| Reduce | El espacio de **control** a la configuración actual | El espacio de **configuración** |
| Ejemplo | Robot diferencial (no se mueve lateralmente) | Tren sobre vías (no toda posición es alcanzable) |

![[Holonomica vs no-holonomica.png]]
*Holonómica vs no-holonómica con ejemplos, slide 8.*

> [!info] Detalle
> Un **tren** ilustra una restricción holonómica: las vías limitan globalmente las posiciones y orientaciones alcanzables. Un robot diferencial ilustra una restricción no-holonómica: en cualquier instante puede estar en cualquier pose $(x, y, \theta)$, pero la velocidad lateral instantánea está prohibida.

## Próximos pasos
- [[Cinemática del Robot Diferencial]] — derivación completa del accionamiento más usado.
- [[Otros Accionamientos - Ackermann, Síncrono, Omni]] — el resto de los tipos.
- [[Odometría y Modelo de Movimiento (Odometría)]] — cómo estimar la pose a partir del giro de las ruedas.

## Fuentes
- `Raw/Diapositivas/Teoricas/03-locomocion-3.pdf` — slides 2–8.

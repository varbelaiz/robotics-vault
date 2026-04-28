---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf
  - Raw/Libro/ProbabilisticRobotics.pdf
ultima_actualizacion: 2026-04-28
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Modelo de Campo de Verosimilitud (Likelihood Field)

> Alternativa eficiente al modelo basado en haz: en vez de mirar a lo largo del rayo, sólo se chequea el punto final contra el obstáculo más cercano.

## Prerequisitos
- [[Modelo de Sensor]] — el hub conceptual de $P(z \mid x, m)$.
- [[Modelo de Sensor Basado en Haz]] — el modelo que esta página mejora.
- [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]] — sensores típicos (sonar, lidar) (M3).

## 1. Motivación: limitaciones del beam-based

El modelo basado en haz tiene dos problemas:
- **Poco suave** para pequeños obstáculos y bordes — pequeñas variaciones de pose dan grandes saltos en $z_{exp}$.
- **Poco eficiente** — requiere ray-casting por haz por pose.

**Idea**: en vez de mirar a lo largo del haz, **chequear sólo el punto final** del haz contra el mapa.

> [!warning] Ad hoc pero funciona (Thrun et al., §6.4)
> El libro es enfático: el likelihood field **carece de una explicación física plausible**. No es una densidad derivada de un modelo generativo del sensor — es un algoritmo *ad hoc* que reemplaza ray-casting por *nearest-neighbor* en 2D. Y sin embargo, **funciona mejor que el beam model en la práctica**. La lección: en robótica probabilística, la fidelidad física no es el único criterio. La **suavidad** del modelo (cambios pequeños de pose → cambios pequeños de verosimilitud) y la **eficiencia** computacional pueden importar más que el realismo, porque permiten algoritmos de hill-climbing más estables y particle filters con menos partículas.

## 2. Mezcla de densidades

La probabilidad es una mezcla de:
- Una **distribución gaussiana** centrada en el **obstáculo más cercano** al punto final del haz.
- Una **distribución uniforme** por mediciones aleatorias.
- Una **distribución uniforme reducida** para mediciones de rango máximo.

Igual que en el beam-based, se asume independencia entre los componentes.

## 3. Ejemplo

Dado un mapa $m$ con un obstáculo y dos puntos finales de haces, el campo de verosimilitud asigna probabilidad alta a regiones cerca del obstáculo:

![[Likelihood - ejemplo.png]]
*Mapa $m$ y campo de verosimilitud correspondiente; la curva $P(z \mid x, m)$ tiene picos suaves cerca de los obstáculos, slide 23.*

## 4. Aplicación a un mapa real

Para el mapa del **Museo Tecnológico de San José**:

![[Likelihood - museo San Jose.png]]
*Izquierda: mapa de grilla de ocupación. Derecha: campo de verosimilitud (regiones claras = mayor probabilidad para puntos finales cerca de obstáculos), slide 24.*

El campo se pre-computa una vez y se consulta como una **tabla 2D** durante la operación — mucho más rápido que ray-casting.

## 5. Scan matching

Una aplicación importante: **scan matching**. Se extrae el campo de verosimilitud de un escaneo y se compara con escaneos previos para detectar movimiento o solapamiento.

![[Likelihood - scan matching.png]]
*Extracción del campo de verosimilitud de un escaneo (puntos negros) y construcción del campo correspondiente, slide 25.*

## 6. Propiedades

- **Muy eficiente**: sólo usa tablas 2D pre-computadas.
- La grilla de distancias es **suave** respecto a pequeñas variaciones en la pose del robot.
- **Permite scan matching**.
- **Ignora las propiedades físicas del haz** (no modela reflexiones especulares, ni el cono del haz, etc.).

![[Likelihood - propiedades.png]]
*Visualización del campo de verosimilitud alrededor de un obstáculo, slide 26.*

> [!warning] Tres limitaciones concretas (Thrun et al., §6.4.2)
> El libro identifica tres flaquezas que conviene tener presente al usar likelihood field:
> 1. **No modela dinámica** — personas u obstáculos móviles no caben naturalmente en el modelo (a diferencia del componente $p_{\text{short}}$ del beam model, que sí los absorbe).
> 2. **"Ve a través de paredes"** — al usar nearest-neighbor en lugar de ray casting, no detecta que un obstáculo intermedio bloquea el haz. Si hay una columna entre el robot y la pared del fondo, el modelo no penaliza una pose donde la columna estaría ocluyendo la pared.
> 3. **No maneja zonas no exploradas** — al asumir todo el mundo como occupied/free, las áreas desconocidas no aportan información correcta. La extensión propuesta en el libro: agregar una tercera categoría **unknown** y tratar las mediciones que caen ahí con probabilidad uniforme $1/z_{\max}$ (lo cual además permite reusar el likelihood field para mapeo, no sólo localización).

## Variantes y conexiones
- [[Modelo de Sensor Basado en Haz]] — el modelo predecesor, más fiel pero más caro.
- [[Modelo de Detección de Landmarks]] — para sensores feature-based.
- [[Mapa de Ocupación]] — el campo de verosimilitud se computa a partir del mapa de ocupación (forward-ref, M6).
- [[MCL - Filtro de Partículas]] — uso típico para localización rápida (forward-ref, M5).

## Fuentes
- `Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf`
  - slide 21 → 1. Motivación: limitaciones del beam-based
  - slide 22 → 2. Mezcla de densidades
  - slide 23 → 3. Ejemplo
  - slide 24 → 4. Aplicación a un mapa real
  - slide 25 → 5. Scan matching
  - slide 26 → 6. Propiedades
- `Raw/Libro/ProbabilisticRobotics.pdf`
  - págs. 139–140 → Motivación: lack of smoothness + ad hoc pero funciona (§6.4.1)
  - págs. 144 → Tres limitaciones (dinámica, ver-a-través-de-paredes, áreas no exploradas) y extensión con categoría unknown (§6.4.2)
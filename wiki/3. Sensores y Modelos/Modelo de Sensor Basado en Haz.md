---
modulo: 3. Sensores y Modelos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[_Overview|← Sensores y Modelos]] | [[Home|← Inicio]]

# Modelo de Sensor Basado en Haz (Beam-Based)

> Modelo probabilístico $p(z \mid x, m)$ que descompone el ruido del sensor en cuatro causas físicas y las mezcla en una densidad por haz.

## Prerequisitos
- [[Sensores Externos - GNSS, Ultrasonido, Lidar, Cámaras]] — qué es un sensor de proximidad (sonar/lidar).
- [[Regla de Bayes]] — base teórica del modelo (forward-ref, M4).

## 1. El problema  *(Teóricas 07-modelos_de_sensores, slide 3)*
Dado un robot en pose $x$ con un mapa $m$ del entorno, ¿cuál es la probabilidad de obtener una medición $z$? Es decir:

$$P(z \mid x, m)$$

Esta probabilidad es el **modelo de observación** que consume cualquier filtro de Bayes para corregir su estimación con datos del sensor.

![[Sensor proximidad - haces.png]]
*Datos típicos de sensores de proximidad: sonar (centro), laser scanner (derecha), slide 3.*

**Método**: tratar de **explicar la medición** con un modelo probabilístico de las causas físicas.

## 2. Modelo basado en haz: independencia  *(Teóricas 07-modelos_de_sensores, slides 4–5)*
Un barrido $z$ consiste de $K$ mediciones individuales:
$$z = \{z_1, z_2, \dots, z_K\}$$

**Suposición**: las mediciones individuales son **independientes** dada la pose del robot. Entonces:

$$P(z \mid x, m) = \prod_{k=1}^{K} P(z_k \mid x, m)$$

Para cada $z_k$ hay que hacer **ray-casting** desde $(x, m)$ para calcular la distancia esperada al obstáculo más cercano y compararla con la medición real.

![[Beam - ray casting.png]]
*Para cada haz individual, comparar la medición con la distancia esperada por ray-casting, slide 5.*

## 3. Causas físicas del error  *(Teóricas 07-modelos_de_sensores, slide 6)*
El error en una medición de distancia se debe a cuatro causas:

1. **Haz reflejado en obstáculos** (medición correcta con ruido).
2. **Haz reflejado en personas o crosstalk** (obstáculo inesperado).
3. **Lectura aleatoria** (interferencia, errores de hardware).
4. **Lectura de distancia máxima** (el haz no detectó nada).

![[Beam - errores medicion.png]]
*Las cuatro causas físicas del error en mediciones de distancia, slide 6.*

## 4. Las cuatro densidades  *(Teóricas 07-modelos_de_sensores, slides 8–9)*

**Hit** — distribución gaussiana centrada en la distancia esperada $z_{exp}$ por el ray-casting:

$$P_{hit}(z \mid x, m) = \eta \, \frac{1}{\sqrt{2\pi b}} \, \exp\left\{-\frac{1}{2}\frac{(z - z_{exp})^2}{b}\right\}$$

**Unexp** — distribución exponencial decreciente para obstáculos inesperados que aparecen **antes** de $z_{exp}$:

$$P_{unexp}(z \mid x, m) = \begin{cases} \eta\,\lambda\,e^{-\lambda z} & z < z_{exp} \\ 0 & \text{en otro caso} \end{cases}$$

![[Beam - hit y unexp.png]]
*Densidades de hit (gaussiana) y unexp (exponencial), slide 8.*

**Max** — pico estrecho cerca de $z_{max}$ (el sensor no detectó nada):

$$P_{max}(z \mid x, m) = \eta\,\frac{1}{z_{small}}$$

**Rand** — distribución uniforme para errores aleatorios:

$$P_{rand}(z \mid x, m) = \eta\,\frac{1}{z_{max}}$$

![[Beam - max y rand.png]]
*Densidades de max (rango máximo) y rand (lectura aleatoria), slide 9.*

## 5. Mezcla de densidades  *(Teóricas 07-modelos_de_sensores, slide 10)*
La densidad final $P(z \mid x, m)$ es una **mezcla ponderada** con pesos $\alpha_{hit}, \alpha_{unexp}, \alpha_{max}, \alpha_{rand}$ que suman 1:

$$P(z \mid x, m) = \begin{pmatrix}\alpha_{hit} \\ \alpha_{unexp} \\ \alpha_{max} \\ \alpha_{rand}\end{pmatrix}^T \cdot \begin{pmatrix} P_{hit}(z \mid x, m) \\ P_{unexp}(z \mid x, m) \\ P_{max}(z \mid x, m) \\ P_{rand}(z \mid x, m) \end{pmatrix}$$

![[Beam - mezcla densidades.png]]
*Mezcla de las cuatro densidades en una distribución resultante, slide 10.*

## 6. Aprendiendo los parámetros  *(Teóricas 07-modelos_de_sensores, slides 11–13)*
Los pesos $\alpha_i$ y los parámetros de cada densidad ($b$, $\lambda$) se aprenden de **datos reales**.

Se recolectan datos crudos del sensor para distancias esperadas conocidas:

![[Beam - datos crudos sonar lidar.png]]
*Mediciones crudas de sonar y lidar para distancia esperada de 300 cm, slide 11.*

Y se ajusta el modelo maximizando la log-verosimilitud:
$$P(z \mid z_{exp})$$

usando hill-climbing, gradient descent o algoritmos genéticos. El último parámetro se calcula deterministicamente para satisfacer la normalización.

![[Beam - resultados aproximacion.png]]
*Aproximación del modelo a datos reales (laser y sonar) para distancias 300 cm y 400 cm, slide 13.*

## 7. Resultado: $p(z \mid x, m)$ sobre el mapa  *(Teóricas 07-modelos_de_sensores, slides 14–15)*
Aplicando el modelo a un escaneo completo en un mapa, se obtiene una **distribución sobre poses**: las poses compatibles con la medición tienen probabilidad alta.

![[Beam - ejemplo Pzxm.png]]
*Escaneo $z$ y la distribución $P(z \mid x, m)$ correspondiente sobre el mapa, slide 14.*

![[Beam - aproximacion 3D.png]]
*Modelos completos para laser y sonar como funciones 2D de distancia esperada y medida, slide 15.*

## 8. Resumen y limitaciones  *(Teóricas 07-modelos_de_sensores, slide 20)*
- Supone **independencia entre haces** (no estrictamente cierto, pero útil).
- Modela explícitamente las causas físicas y mezcla sus densidades.
- Implementación: aprender parámetros de datos reales, hacer ray-casting para distancias esperadas, posiblemente usar modelos distintos para distintos ángulos al obstáculo.
- Las distancias esperadas pueden **pre-procesarse** para acelerar la evaluación.

> [!warning] Costo computacional
> El beam-based requiere ray-casting por cada haz por cada pose hipotética. Para particle filters con miles de partículas, esto es caro. La alternativa eficiente es el [[Modelo de Campo de Verosimilitud]].

## Variantes y conexiones
- [[Modelo de Campo de Verosimilitud]] — alternativa más eficiente y suave.
- [[Modelo de Detección de Landmarks]] — para sensores que detectan marcadores en lugar de distancias continuas.
- [[MCL - Filtro de Partículas]] — el principal consumidor (forward-ref, M5).

## Fuentes
- `Raw/Diapositivas/Teoricas/07-modelos_de_sensores-3.pdf` — slides 1–20.

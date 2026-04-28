---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
  - Raw/Libro/ProbabilisticRobotics.pdf
ultima_actualizacion: 2026-04-28
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Introducción a la Robótica Probabilística

> Representación explícita de la incertidumbre usando cálculo de probabilidades. "Tratemos de saber lo que no sabemos."

## Idea central

La robótica probabilística modela explícitamente la incertidumbre inherente en:
- **Sensores ruidosos**: mediciones imperfectas, obstáculos inesperados, crosstalk
- **Movimiento impreciso**: deslizamiento de ruedas, errores de odometría
- **Entornos parcialmente observables**: no todo el entorno es visible simultáneamente

En lugar de tratar de eliminar la incertidumbre, la representamos como distribuciones de probabilidad y la actualizamos de forma sistemática a medida que el robot percibe y actúa.

> [!info] Las 5 fuentes de incertidumbre (Thrun et al., §1.1)
> El libro identifica cinco causas concurrentes que justifican el tratamiento probabilístico:
> 1. **Entornos** — los mundos físicos son intrínsecamente impredecibles; entornos abiertos (rutas, hogares) son mucho más dinámicos que las celdas estructuradas de fábrica.
> 2. **Sensores** — limitados por física (resolución, alcance) y por ruido; no es sólo que midan mal, sino que su información sobre el estado es siempre parcial.
> 3. **Actuadores** — motores reales tienen ruido de control y desgaste; cualquier $u_t$ produce un $x_t$ con dispersión.
> 4. **Modelos** — son abstracciones: capturan parcialmente el proceso real. El error de modelo se ignoraba tradicionalmente, pero está siempre presente.
> 5. **Computación** — los algoritmos son aproximados porque deben correr en tiempo real; muchos sacrifican exactitud para responder a tiempo.

## ¿Por qué probabilístico?

Los sistemas robóticos autónomos deben estimar su estado (posición, orientación, etc.) a partir de datos ruidosos. El enfoque probabilístico permite:

1. **Cuantificar la confianza**: no solo un valor, sino una distribución de posibilidades
2. **Combinar evidencia**: fusionar múltiples mediciones de forma coherente
3. **Incorporar acciones**: actualizar creencias cuando el robot se mueve
4. **Tomar decisiones**: elegir acciones basadas en la incertidumbre cuantificada

> [!quote] Conjetura central (Thrun et al., §1.3)
> *"Un robot que tiene noción de su propia incertidumbre y actúa en consecuencia es superior a uno que no la tiene."*

## Profundización: implicaciones (Thrun et al., §1.3)

El enfoque probabilístico paga su poder con dos costos:

- **Ineficiencia computacional** — representar densidades enteras es estrictamente más caro que mantener un único *best guess*.
- **Necesidad de aproximar** — en mundos continuos, la posterior exacta tiene infinitas dimensiones. Hay que comprometerse con representaciones tratables: gaussianas (paramétrico, unimodal), grillas (no paramétrico, multimodal grueso) o partículas (no paramétrico, *any-time*).

A cambio, los algoritmos probabilísticos son los **únicos métodos conocidos** para problemas como:
- *Kidnapped robot* — recuperarse de un fallo de localización catastrófico.
- Mapeo de entornos grandes sin GPS.
- Aprendizaje robótico visto como estimación a largo plazo.

Y degradan suavemente cuando los modelos son imperfectos — algo que los métodos no probabilísticos suelen no hacer.

## Conceptos que se necesitan

- [[Axiomas de Probabilidad]] — fundamentos formales
- [[Variables Aleatorias Discretas]] — espacios de estado discretos
- [[Variables Aleatorias Continuas]] — densidades en espacios continuos
- [[Probabilidad Conjunta]] — distribuciones multivariadas
- [[Probabilidad Condicional]] — condicionamiento
- [[Regla de Bayes]] — actualización con evidencia
- [[Suposición de Markov]] — dependencia temporal local
- [[Filtro de Bayes]] — algoritmo recursivo de estimación

## Fuentes
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf`
  - pág. 2 → Idea central — *"Tratemos de saber lo que no sabemos"*
- `Raw/Libro/ProbabilisticRobotics.pdf`
  - págs. 1–2 → Las 5 fuentes de incertidumbre (§1.1)
  - págs. 3–5 → Idea central + ejemplo de localización (§1.2)
  - págs. 5–7 → Conjetura central + implicaciones (§1.3)
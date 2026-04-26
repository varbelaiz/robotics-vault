---
modulo: 4. Robótica Probabilística
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf
  - Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf
ultima_actualizacion: 2026-04-26
---

> [[Robótica Probabilística|← Robótica Probabilística]] | [[Robotica|← Inicio]]

# Introducción a la Robótica Probabilística

> Representación explícita de la incertidumbre usando cálculo de probabilidades. "Tratemos de saber lo que no sabemos."

## Idea central  *(05-intro_robo_proba parte 1, pág. 2)*

La robótica probabilística modela explícitamente la incertidumbre inherente en:
- **Sensores ruidosos**: mediciones imperfectas, obstáculos inesperados, crosstalk
- **Movimiento impreciso**: deslizamiento de ruedas, errores de odometría
- **Entornos parcialmente observables**: no todo el entorno es visible simultáneamente

En lugar de tratar de eliminar la incertidumbre, la representamos como distribuciones de probabilidad y la actualizamos de forma sistemática a medida que el robot percibe y actúa.

## ¿Por qué probabilístico?

Los sistemas robóticos autónomos deben estimar su estado (posición, orientación, etc.) a partir de datos ruidosos. El enfoque probabilístico permite:

1. **Cuantificar la confianza**: no solo un valor, sino una distribución de posibilidades
2. **Combinar evidencia**: fusionar múltiples mediciones de forma coherente
3. **Incorporar acciones**: actualizar creencias cuando el robot se mueve
4. **Tomar decisiones**: elegir acciones basadas en la incertidumbre cuantificada

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
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_1.pdf` — págs. 1–6
- `Raw/Diapositivas/Teoricas/05-intro_robo_proba-parte_2.pdf` — pág. 45 (resumen)

---
modulo: 1. Fundamentos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/00-introduccion-3.pdf
ultima_actualizacion: 2026-04-26
---

> [[Fundamentos|← Fundamentos]] | [[Robotica|← Inicio]]

# Introducción a la Robótica Móvil

> Panorama del campo: problemas, herramientas, historia, y estado actual de la robótica autónoma.

## Objetivo del curso  *(00-introduccion, págs. 4–6)*

Introducción a los problemas centrales de la robótica móvil:

- **Modelado**: representar el robot, el entorno, y la incertidumbre
- **Localización y Mapeo**: saber dónde estoy y cómo es el entorno
- **Planeamiento de trayectorias**: calcular rutas del punto A al B
- **Herramienta transversal**: razonamiento probabilístico para trabajar con datos ruidosos e incertezas
- **Implementación**: Python y ROS

El libro de referencia es **"Probabilistic Robotics"** de Thrun, Burgard y Fox.

### Contenidos del curso  *(00-introduccion, pág. 5)*

1. Paradigmas de control de robots móviles
2. Sensores de proximidad
3. Robótica probabilística — Probabilidad, Bayes
4. Modelos de sensores
5. Modelos de movimiento
6. Filtros Bayesianos — Filtro Discreto
7. Filtros Bayesianos — Filtro de Partículas — Montecarlo
8. Filtros Bayesianos — Filtro de Kalman, EKF, UKF
9. Generación de Mapas
10. SLAM — Mapeo y Localización Simultánea
11. Planeamiento de trayectorias
12. Exploración

## Problemas de la robótica  *(00-introduccion, págs. 7–8)*

Los problemas que aborda la robótica se dividen en:

- **Navegación**: moverse autónomamente en el entorno
- **Percepción**: interpretar datos de sensores
- **Planeamiento**: calcular acciones y trayectorias
- **Aprendizaje**: mejorar el comportamiento con experiencia
- **Cooperación**: múltiples robots trabajando juntos
- **Actuación e interacción**: manipulación, agarre, motricidad fina
- **Razonamiento**: tomar decisiones bajo incertidumbre

## Arquitectura de sistemas robóticos autónomos  *(00-introduccion, pág. 8)*

![[Robotica - ciclo percibir-modelar-actuar.png]]
*Ciclo percibir → modelar → actuar de un sistema robótico autónomo.*

Un robot autónomo debe:
1. **Percibir** el entorno mediante sensores
2. **Construir un modelo** del entorno y de su propio estado
3. **Generar acciones** para alcanzar sus objetivos

## Historia  *(00-introduccion, págs. 9–10)*

- **1808–1840**: primeros autómatas mecánicos
- **1921**: la palabra "robot" aparece por primera vez en la obra de Karl Capek *R.U.R.* (Rossum's Universal Robots), derivada del checo *robota* (esclavo, trabajo forzado)

## Tendencias actuales  *(00-introduccion, págs. 11–18)*

Los robots están saliendo de las celdas de fábrica hacia nuevos ámbitos:

- Entretenimiento y juguetes
- Servicios personales y domésticos
- Medicina y cirugía
- Automatización industrial (minería, cosecha)
- Entornos peligrosos (espacio, submarino)
- Vehículos autónomos de transporte
- Logística y depósitos (Amazon, Correo Argentino)
- Respuesta a desastres naturales

![[Robotica - domesticos 2010 vs 2024.png]]
*Evolución de robots domésticos: 2010 vs 2024.*

## Robots que caminan  *(00-introduccion, págs. 22–26)*

![[Robotica - Boston Dynamics caminando.png]]
*Robots bípedos de Boston Dynamics.*

La robótica de locomoción ha avanzado rápidamente desde prototipos académicos hasta robots humanoides funcionales que corren, suben escaleras, y se recuperan de caídas.

## Androides y el _uncanny valley_  *(00-introduccion, págs. 27–28)*

El efecto del "uncanny valley" describe la respuesta negativa que provocan androides casi humanos: a mayor parecido humano, mayor incomodidad — excepto cuando la similitud es casi perfecta.

## Competencias DARPA  *(00-introduccion, págs. 29–34)*

Las competencias DARPA marcaron hitos clave en la navegación autónoma:

- **Grand Challenge (2003, 2005)**: vehículos autónomos en desierto. Stanley (Stanford) ganó en 2005.
- **Urban Challenge (2007)**: navegación en entorno urbano con tráfico.
- **Robotics Challenge (2015)**: robots humanoides en escenarios de desastre.
- **Sub-T Challenge (2021)**: navegación en túneles subterráneos.

## Vehículos autónomos comerciales  *(00-introduccion, págs. 33–35)*

- Google/Waymo: desarrollo continuo desde 2009
- Cruise y Tesla: servicios de Robotaxi en Austin, TX y San Francisco, CA

## Robótica en UdeSA  *(00-introduccion, págs. 37–38)*

La cátedra se enmarca en el **Track de Robótica** de la carrera de Ing. en Sistemas (IIA, UdeSA). El laboratorio **LINAR** (Laboratorio de Inteligencia Artificial y Robótica) desarrolla investigación en el área.

Correlatividades:
- Mecatrónica I → Visión, Sistemas de Control → Principios de la Robótica Autónoma
- Robótica Avanzada, Aprendizaje por Refuerzo → posibilitadores

## Conexiones
- [[Paradigmas de Control de Robots]] — cómo ha evolucionado la arquitectura de control
- [[Introducción a la Robótica Probabilística]] — el enfoque que domina la materia
- [[2. Locomoción/Locomoción|Módulo 2]] — cómo se mueven los robots
- [[3. Sensores y Modelos/Sensores y Modelos|Módulo 3]] — cómo perciben

## Fuentes
- `Raw/Diapositivas/Teoricas/00-introduccion-3.pdf` — págs. 1–38

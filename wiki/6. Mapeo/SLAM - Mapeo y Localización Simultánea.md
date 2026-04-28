---
modulo: 6. Mapeo
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf
  - Raw/Libro/ProbabilisticRobotics.pdf
ultima_actualizacion: 2026-04-28
---

> [[Mapeo|← Mapeo]] | [[Robotica|← Inicio]]

# SLAM - Mapeo y Localización Simultánea

> Estimar simultáneamente el mapa del entorno y la posición del robot.

## 1. El problema

- **Mapeo con poses conocidas** es el caso simplificado donde el robot sabe exactamente dónde está
- En el caso real, el robot **no conoce su posición** — debe estimarla junto con el mapa
- Problema **acoplado**: necesitas el mapa para localizarte, y tu posición para mapear

> [!info] Online SLAM vs Full SLAM (Thrun et al., §10.1)
> El libro distingue dos formulaciones del problema:
> - **Online SLAM**: estima sólo la pose actual + el mapa, $p(x_t, m \mid z_{1:t}, u_{1:t})$. Es incremental — descarta mediciones y controles ya procesados. EKF-SLAM resuelve este caso.
> - **Full SLAM**: estima la **trayectoria completa** + el mapa, $p(x_{1:t}, m \mid z_{1:t}, u_{1:t})$. Permite reprocesar cualquier pose pasada cuando aparece nueva información — clave para *loop closure*. GraphSLAM, FastSLAM y SAM resuelven esta versión.
>
> El online se obtiene marginalizando past poses del full. La diferencia computacional es enorme: el state vector del full crece con $t$ y por eso ningún algoritmo $O(N^2)$ tipo EKF puede correr full SLAM en tiempo real. Los algoritmos full usan estructura de grafo (sparse) o partículas para escalar.

## 2. Enfoques de SLAM
- **EKF-SLAM**: usa [[EKF]] con features como landmarks
- **Particle Filter SLAM (FastSLAM)**: usa [[MCL - Filtro de Partículas]] para la pose y filtros individuales por feature
- **Mapas de ocupación + MCL**: mapa volumétrico + filtro de partículas para localización

> [!info] El "milagro" del EKF-SLAM (Thrun et al., §10.2.2)
> Una propiedad fundamental que el libro destaca: en EKF-SLAM, **observar un solo landmark mejora la estimación de TODOS los landmarks del mapa** — no sólo del observado. La razón vive en las correlaciones off-diagonal de la matriz de covarianza $\Sigma$:
> 1. Una observación reduce la incertidumbre de la pose del robot.
> 2. La pose del robot está correlacionada con todos los landmarks (porque cada landmark fue observado *desde* alguna pose).
> 3. Esa correlación, capturada en $\Sigma$, propaga la mejora a todo el mapa.
>
> Esto es lo que hace al EKF-SLAM funcionar para *loop closure* sin necesidad de re-estimar trayectorias pasadas: la matriz $\Sigma$ ya guarda toda la información que une el presente con el pasado. Es también la razón por la que la matriz $\Sigma$ se vuelve **densa** rápidamente — todas las variables terminan correlacionadas — lo cual explica el costo $O(N^2)$ por update.

## 3. Limitaciones de EKF-SLAM y técnicas prácticas (Thrun et al., §10.2–§10.3)

EKF-SLAM fue históricamente el primer algoritmo SLAM exitoso, pero tiene limitaciones serias que motivan técnicas más modernas:

### Limitaciones estructurales

- **Complejidad cuadrática** — cada update cuesta $O(N^2)$ donde $N$ es el número de landmarks. Limita el algoritmo a mapas con menos de ~1.000 features. Para mapas densos ($10^6$+ features típicos en SLAM moderno) el EKF deja de ser aplicable.
- **Sólo online SLAM** — agregar past poses al state vector haría que crezca sin cota. EKF no puede hacer *full SLAM*.
- **Brittleness ante ambigüedad** — el data association se hace con **maximum likelihood greedy** (asignar cada feature al landmark más probable). Una vez hecha la asignación, no se revisa. Si el ML está equivocado (e.g. dos landmarks parecidos cerca uno del otro), el filtro propaga el error y nunca se recupera.

### Técnicas para manejo robusto de mapas

El libro lista mecanismos prácticos que toda implementación seria de EKF-SLAM incluye:

- **Provisional landmark list** — un nuevo landmark candidato no se agrega al mapa principal hasta verlo $N$ veces consistentemente. Mientras tanto vive en una lista provisional que no afecta la pose. Filtra outliers espurios.
- **Landmark evidence counter** — un contador en log-odds $o_j$ por landmark: incrementa cuando se observa, decrementa cuando *debería* verse pero no se ve. Si baja del threshold, el landmark se elimina del mapa. Maneja landmarks que aparecen / desaparecen.
- **Spatial arrangement** — elegir landmarks suficientemente separados entre sí para reducir confusión. Tradeoff: pocos landmarks → mala localización; demasiados → confusión por proximidad.
- **Signatures** — maximizar distintividad perceptual (puertas con colores distintos, pasillos con anchos distintos). Cuanto más distinguibles los landmarks, mejor anda el data association.

### ¿Por qué seguir cubriéndolo?

El valor del EKF-SLAM hoy es histórico y didáctico: muestra **cómo emerge el problema de SLAM** y **por qué necesita correlaciones globales**. Los algoritmos modernos (GraphSLAM, FastSLAM, ORB-SLAM) corrigen sus limitaciones pero comparten esa estructura conceptual.

## 4. Conexiones
- [[Mapeo con Poses Conocidas]] — versión simplificada (poses conocidas)
- [[MCL - Filtro de Partículas]] — usado para localización dentro de SLAM
- [[EKF]] — otra aproximación (EKF-SLAM)
- [[Features vs Mapas Volumétricos]] — elección de representación afecta el enfoque

## Fuentes
- `Raw/Diapositivas/Teoricas/12-mapas_de_ocupacion-2.pdf`
  - págs. 3–6 → 1. El problema (planteo de mapeo, antes de fijar poses conocidas)
- `Raw/Libro/ProbabilisticRobotics.pdf`
  - págs. 245–247 → Online vs Full SLAM (§10.1)
  - págs. 248–252 → EKF-SLAM con known correspondence + efecto de correlación global (§10.2.2)
  - págs. 256–264 → EKF-SLAM con unknown correspondence + map management + limitaciones (§10.3)

> [!info] Página puente
> SLAM no se desarrolla a fondo en este curso. Esta página es una landing que conecta los enfoques (EKF-SLAM, FastSLAM) con sus bases en M5/M6. El libro Thrun et al. (cap 10) es la referencia para estudiar EKF-SLAM en detalle si se necesita.
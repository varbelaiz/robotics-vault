> [[Mapeo|← Mapeo]] | [[Robotica|← Inicio]]

# Módulo 6 — Mapeo

> Construir una representación del entorno: mapas de ocupación, SLAM, y exploración.

## Por qué este módulo
Los mapas permiten localización, navegación eficiente, y ejecución de tareas. El módulo cubre desde la representación más simple (grilla de ocupación) hasta el problema completo de [[SLAM - Mapeo y Localización Simultánea]].

> [!info] Los 4 factores de dificultad del mapeo (Thrun et al., §9.1)
> No todos los problemas de mapeo son igualmente difíciles. El libro identifica cuatro factores que dominan la dificultad y motivan los distintos enfoques que cubre este módulo:
> 1. **Tamaño** — cuánto más grande el entorno relativo al alcance perceptual del robot, más caro mapear.
> 2. **Ruido** — en sensores y actuadores. Sin ruido, mapeo sería trivial; con ruido alto, los algoritmos deben modelarlo cuidadosamente.
> 3. **Ambigüedad perceptual** — lugares que se ven iguales (pasillos simétricos, oficinas idénticas) hacen muy difícil establecer correspondencias entre observaciones distintas.
> 4. **Ciclos** — cerrar un loop al volver a un lugar visitado acumula error odométrico que debe corregirse retroactivamente. Es el escenario más difícil y el que motiva el SLAM full (no online).

## Recorrido sugerido
1. [[Mapas de Ocupación]] — representación probabilística celda por celda.
2. [[Mapas de Grilla]] — estructura rígida, celdas ocupadas/libres.
3. [[Mapeo con Poses Conocidas]] — problema simplificado.
4. [[SLAM - Mapeo y Localización Simultánea]] — problema completo.
5. [[Features vs Mapas Volumétricos]] — dos enfoques de representación.
6. [[Exploración]] — descubrir entornos desconocidos.

## Conecta con
- ⬅️ [[3. Sensores/Sensores|Módulo 3: Sensores]] (mediciones para mapear)
- ⬅️ [[4. Robótica Probabilística/Robótica Probabilística|Módulo 4: Probabilidad]] (mapeo probabilístico)
- ⬅️ [[5. Filtros Bayesianos/Filtros Bayesianos|Módulo 5: Filtros Bayesianos]] (localización dentro de SLAM)

## Páginas en este módulo
- [[Mapas de Ocupación]]
- [[Mapas de Grilla]]
- [[Mapeo con Poses Conocidas]]
- [[SLAM - Mapeo y Localización Simultánea]]
- [[Features vs Mapas Volumétricos]]
- [[Exploración]]

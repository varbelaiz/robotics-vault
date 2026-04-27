---
modulo: 1. Fundamentos
estado: completo
fuentes:
  - Raw/Diapositivas/Teoricas/02-paradigmas-4.pdf
ultima_actualizacion: 2026-04-26
---

> [[Fundamentos|← Fundamentos]] | [[Robotica|← Inicio]]

# Paradigmas de Control de Robots

> Evolución de las arquitecturas de control: jerárquico → reactivo → híbrido, con énfasis en el paradigma basado en comportamientos y los campos potenciales.

## 1. Paradigma Clásico (Jerárquico)

![[Paradigmas - clasico jerarquico.png]]
*Paradigma clásico: Sensar → Planear → Actuar.*

El enfoque original, nacido en los años 70, se basa en tres etapas secuenciales:

1. **Sensar**: recolectar datos del entorno
2. **Planear**: construir un modelo completo del mundo y calcular un plan
3. **Actuar**: ejecutar el plan

### Ejemplos históricos

- **STRIPS** (Stanford Research Institute Problem Solver, 1972): asume un mundo cerrado y limitado con modelo perfecto
- **Stanford Cart** (1973): tomaba 9 imágenes, identificaba puntos interesantes, integraba en un modelo global, correlacionaba con imágenes previas, y solo entonces decidía moverse

### Descomposición funcional

![[Paradigmas - descomposicion funcional.png]]
*El paradigma clásico como descomposición funcional en capas.*

Cada capa depende de la inferior y produce datos para la superior. El problema: un fallo en cualquier capa se propaga al sistema completo.

**Limitaciones**:
- Requiere modelo global del mundo (costoso en computación)
- No tolera fallos bien — si la percepción falla, el plan es inútil
- Latencia alta: esperar a completar percepción + planificación antes de actuar

## 2. Paradigma Reactivo (Basado en Comportamientos)

![[Paradigmas - reactivo vertical.png]]
*Paradigma reactivo: descomposición vertical, Sensar → Actuar directo.*

Inspirado en sistemas biológicos. La premisa central: **"el mundo mismo es su mejor modelo"**.

### Características
- **Agente situado**: el robot es parte integral de su entorno
- **Sin memoria**: controlado por lo que sucede en cada instante
- **Alto acoplamiento percepción-acción**: los comportamientos mapean directamente sensores → motores
- **Representación egocéntrica**: solo existe sensado local, específico a cada comportamiento

### Comportamientos

Un comportamiento es un mapeo directo de datos de sensores a un patrón de acciones motoras tendiente a realizar una tarea. Son:
- Los bloques básicos de acción del robot
- Modulares (permiten desarrollo incremental)
- Emergentes (el comportamiento general surge de la interacción de comportamientos simples)

## 3. Arquitectura de Subsunción

Introducida por Rodney Brooks (1986). Los comportamientos son redes de nodos sensado-actuación (AFSM — augmented finite state machines). Los módulos se agrupan en **capas de competencia**, donde una capa superior puede **subsumir** (anular) las inferiores.

**No hay estado interno.** Cada capa tiene acceso directo a los sensores.

### Ejemplo: navegación en corredor

![[Paradigmas - arquitectura subsumption.png]]
*Arquitectura de subsumption de Brooks: capas de comportamiento.*

![[Paradigmas - nivel0 colision.png]]
*Nivel 0: comportamiento de colisión — detenerse al detectar obstáculo cercano.*

![[Paradigmas - nivel0 evasion.png]]
*Nivel 0: comportamiento de evasión — alejarse de obstáculos.*

![[Paradigmas - nivel1 movimiento aleatorio.png]]
*Nivel 1: movimiento aleatorio — cambiar orientación al no haber estímulos.*

![[Paradigmas - nivel2 seguir corredor.png]]
*Nivel 2: seguir corredor — ir por el medio del pasillo.*

## 4. Campos Potenciales

Los robots se modelan como partículas bajo la influencia de un **campo potencial** vectorial:

- Siguen los gradientes de potencial
- El campo depende de obstáculos, dirección deseada y objetivos
- El campo resultante es suma de campos básicos (**primitivas**)

### Primitivas de campos potenciales

![[Paradigmas - campos potenciales primitivas.png]]
*Primitivas de campos potenciales: uniforme, atractivo, repulsivo, perpendicular, tangencial.*

- **Uniforme**: movimiento rectilíneo constante
- **Atractivo**: atrae hacia el objetivo
- **Repulsivo**: repele desde los obstáculos
- **Perpendicular**: mantiene distancia paralela a una pared
- **Tangencial**: sigue la superficie de un obstáculo

### Ejemplo: seguir un corredor

Se combinan campos de tres niveles:
1. Nivel 0 (evasión): fuerzas repulsivas desde obstáculos detectados
2. Nivel 1 (moverse): campo uniforme
3. Nivel 2 (seguimiento): dos campos perpendiculares + uno uniforme

### Problema de mínimos locales

![[Paradigmas - minimos locales.png]]
*El robot queda atrapado en un mínimo local del campo potencial.*

El robot puede quedar atascado cuando las fuerzas atractivas y repulsivas se cancelan en un punto que no es el objetivo.

**Estrategias de escape**:
- Backtracking
- Movimiento aleatorio para escapar del mínimo
- Planeamiento (seguimiento de paredes)
- Aumentar el potencial de regiones ya visitadas

### Características generales

- ✅ Fácil de visualizar y combinar
- ✅ No requiere prioridad entre niveles
- ❌ Problema de mínimos locales
- ❌ Necesita alta tasa de actualización
- ❌ Muy dependiente del sintonizado de parámetros

## 5. Paradigma Híbrido

![[Paradigmas - hibrido.png]]
*Paradigma híbrido: combina planificación deliberativa con control reactivo.*

Combina lo mejor de ambos mundos:
- **Modelo del mundo** para planificación a largo plazo
- **Control reactivo** para respuesta rápida a eventos inesperados

## 6. ROS como arquitectura de software

ROS (Robot Operating System) implementa una arquitectura distribuida basada en nodos que refleja el paradigma híbrido:

- **Nodos**: unidades de ejecución independientes
- **Topics**: canales de comunicación asíncrona
- **Mensajes**: tipos de datos estandarizados
- **Pub/Sub**: modelo de publicación/suscripción para comunicación descentralizada

Ventajas de ROS:
- No reinventar la rueda (drivers, herramientas, estándares)
- Comunidad y desarrollo abierto
- Herramientas de visualización (rviz) y debugging
- Desarrollo asincrónico de software

## Conexiones
- [[Introducción a la Robótica Móvil]] — contexto del campo
- [[ROS2 - Conceptos Base]] — implementación práctica de la arquitectura distribuida
- [[4. Robótica Probabilística/Robótica Probabilística|Módulo 4]] — el enfoque probabilístico que supera las limitaciones de los paradigmas clásicos

## Fuentes
- `Raw/Diapositivas/Teoricas/02-paradigmas-4.pdf`
  - págs. 1–6 → 1. Paradigma Clásico (Jerárquico)
  - págs. 7–11 → 2. Paradigma Reactivo (Basado en Comportamientos)
  - págs. 12–16 → 3. Arquitectura de Subsunción
  - págs. 17–28 → 4. Campos Potenciales
  - págs. 29–30 → 5. Paradigma Híbrido
  - págs. 31–40 → 6. ROS como arquitectura de software
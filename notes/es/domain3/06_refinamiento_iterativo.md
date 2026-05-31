# Técnicas de Refinamiento Iterativo

**Peso en el examen:** Parte del Dominio 3 – Configuración y Flujos de Trabajo de Claude Code (20%)

---

## Jerarquía de Tres Técnicas

Elegir la técnica basándose en el **problema específico**, no en la complejidad:

### 1. Ejemplos Concretos (para interpretación inconsistente)

**Disparador:** Las descripciones en prosa producen resultados variables entre invocaciones.

**Solución:** Proporcionar 2–3 pares específicos de entrada/salida.

> "La solución no es más prosa. La solución son ejemplos concretos."

El modelo generaliza patrones de ejemplos de manera más confiable que de texto descriptivo. Incluir el *porqué* detrás de cada ejemplo, no solo pares de entrada/salida.

---

### 2. Iteración Guiada por Pruebas (para transformaciones complejas)

**Disparador:** La transformación está bien entendida pero es compleja.

**Solución:** Escribir pruebas que cubran caminos felices, casos extremos y requisitos de rendimiento. Compartir fallos de prueba con mensajes específicos ("Se esperaba X, se obtuvo Y") — elimina la ambigüedad de interpretación.

---

### 3. Patrón de Entrevista (para dominios desconocidos)

**Disparador:** El desarrollador carece de experiencia en el dominio y podría omitir consideraciones importantes.

**Solución:** Hacer que Claude haga preguntas de aclaración *antes* de implementar. Saca a la luz consideraciones como estrategias de invalidación de caché o requisitos de consistencia.

---

## Estrategia de Entrega de Retroalimentación

| Tipo de Retroalimentación | Enfoque |
|---|---|
| Problemas interdependientes | Agrupar en un mensaje — el modelo ve todas las restricciones simultáneamente |
| Problemas independientes | Secuencial — corregir uno a la vez para evitar confusión |

---

## Distinción Clave

> **Patrón de entrevista** = dominio desconocido donde el desarrollador podría omitir consideraciones importantes.
>
> **Ejemplos concretos** = el desarrollador conoce la transformación exacta pero el modelo la interpreta inconsistentemente.

---

## Puntos Clave del Examen

- Más prosa precisa **no** soluciona problemas de consistencia — usar ejemplos concretos
- Los ejemplos deben incluir el *porqué* (enseña generalización, no coincidencia literal de patrones)
- El patrón de entrevista es para **lagunas de conocimiento del desarrollador**, no limitaciones del modelo
- Agrupar retroalimentación para problemas interdependientes; secuencial para independientes

---

## Trampas Comunes del Examen

- Seleccionar "instrucciones más detalladas" como solución cuando el problema es salida inconsistente
- Confundir el patrón de entrevista con técnicas basadas en ejemplos
- Proporcionar pares de entrada/salida sin razonamiento (el modelo no puede generalizar)
- Agrupar toda la retroalimentación independientemente de si los problemas son interdependientes

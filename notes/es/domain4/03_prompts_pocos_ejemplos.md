# Prompts con Pocos Ejemplos (Few-Shot)

**Peso en el examen:** Dominio 4 – Ingeniería de Prompts y Salida Estructurada (20%)

---

## Principio Central

> Los ejemplos few-shot son la **técnica más efectiva** para lograr salida consistente y bien formateada de Claude.

Esto tiene prioridad sobre instrucciones adicionales, ajustes de confianza o modificaciones de temperatura.

---

## Cuándo Usar Ejemplos Few-Shot

| Disparador | Descripción |
|---|---|
| Inconsistencia de formato | Existen instrucciones detalladas pero producen estructuras variables (listas, tablas, prosa) |
| Juicios ambiguos | El modelo hace clasificaciones inconsistentes en casos límite |
| Lagunas de extracción | La información existe en documentos pero en formatos inesperados (narrativas, párrafos incrustados) → campos vacíos/nulos |

---

## Reglas de Construcción

### Cantidad Óptima
**2–4 ejemplos dirigidos.**
- Menos de 2: no establecen patrones
- Más de 4: desperdician tokens sin ganancias proporcionales

### Incluir Razonamiento
Los ejemplos deben mostrar el **POR QUÉ** se tomaron las decisiones, no solo pares de entrada/salida.

Esto enseña generalización a escenarios nuevos en lugar de coincidencia literal de patrones.

### Dirigir los Escenarios Fallidos
Si la extracción funciona en tablas pero falla en narrativas, los ejemplos deben demostrar específicamente **extracción de narrativas** — no los casos fáciles.

---

## Comparación de Técnicas

| Problema | Solución |
|---|---|
| Formato inconsistente | Ejemplos few-shot |
| JSON malformado | `tool_use` con esquemas |
| Valores faltantes fabricados | Campos opcionales/anulables |
| Lagunas de extracción (formatos inesperados) | Ejemplos few-shot |
| Fallos de validación de sumas | Bucles de validación-reintento |
| Juicios ambiguos inconsistentes | Ejemplos few-shot con razonamiento |

---

## Beneficios Secundarios

- Reduce alucinaciones en tareas de extracción al mostrar cómo manejar la variedad estructural
- Reduce falsos positivos en revisión de código al distinguir patrones aceptables de problemas genuinos

---

## Puntos Clave del Examen

- Ejemplos few-shot > instrucciones más detalladas para problemas de consistencia
- **2–4 ejemplos** es el conteo óptimo
- Siempre incluir **razonamiento** en los ejemplos — no solo pares de E/S
- Dirigir el **escenario específico fallido** (narrativas, no casos fáciles de tablas)
- Los ejemplos few-shot enseñan principios, no patrones

---

## Trampas Comunes del Examen

- Asumir que instrucciones más detalladas solucionan problemas de consistencia (no lo hacen)
- Tratar los ejemplos few-shot como coincidencia pura de patrones (deben incluir razonamiento)
- Usar umbrales de confianza para abordar inconsistencia de juicio (usar ejemplos en su lugar)
- Proporcionar más de 4 ejemplos esperando mejores resultados (rendimientos decrecientes + desperdicio de tokens)

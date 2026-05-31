# Hooks del SDK de Agentes

**Peso en el examen:** Dominio 1 – Arquitectura Agéntica y Orquestación (27%)

---

## ¿Qué Son los Hooks del SDK?

Los hooks del SDK de agentes inyectan **comportamiento determinista** en la toma de decisiones probabilística del modelo, interceptando llamadas a herramientas y resultados para aplicar reglas de negocio y normalizar datos.

---

## Dos Tipos de Hooks

### Hooks PostToolUse (Después de la Ejecución)
- Se ejecutan **después** de que una herramienta se ejecuta, antes de que el modelo procese los resultados
- Transforman y normalizan las salidas de herramientas en formatos consistentes
- **No pueden bloquear** acciones — la herramienta ya se ejecutó

**Casos de uso:**
- Convertir timestamps Unix a ISO 8601
- Convertir códigos numéricos a cadenas legibles
- Normalizar formatos heterogéneos de salida de herramientas

### Hooks de Intercepción de Llamada de Herramienta (Antes de la Ejecución)
- Se ejecutan **antes** de que una herramienta se ejecute
- Pueden bloquear, modificar o redirigir llamadas de herramientas salientes
- Previenen completamente que ocurran acciones no conformes

**Casos de uso:**
- Validación de umbral de reembolso (bloquear montos >$500)
- Puertas de prerrequisito de cumplimiento (verificaciones AML antes de transferencias)
- Flujos de aprobación de gerente para operaciones de alto valor

---

## Marco de Decisión

| Requisito | Mecanismo | Garantía |
|---|---|---|
| Debe seguirse 100% del tiempo | Hooks (pre-ejecución) | Determinista |
| Desviación ocasional aceptable | Prompts | Probabilístico |
| Normalización de datos post-herramienta | Hooks (post-ejecución) | Determinista |

**Regla crítica:** Usar hooks cuando un solo fallo causa pérdida financiera o riesgo legal. Usar prompts para preferencias de formato y guías de estilo.

---

## Hooks de Ciclo de Vida para Subagentes

El SDK de agentes de Claude proporciona:
- **SubagentStart** — se dispara cuando se instancian subagentes; útil para limitación de tasa y validación
- **SubagentStop** — se dispara al completarse el subagente; habilita validación de salida y eliminación de datos sensibles

Los subagentes pueden definir hooks PreToolUse/PostToolUse con alcance que afectan solo su ejecución.

---

## Puntos Clave del Examen

- Los hooks PostToolUse **no pueden bloquear** acciones — se ejecutan después
- Los hooks de pre-ejecución (intercepción) son la **única** forma de garantizar cumplimiento del 100%
- Las instrucciones de prompts no pueden garantizar cumplimiento para requisitos regulatorios
- PostToolUse = transformación de datos; Pre-ejecución = aplicación de políticas
- El SDK también proporciona hooks SubagentStart/SubagentStop

---

## Trampa Común del Examen

> "Necesitas garantizar que las verificaciones AML se completen antes de las transferencias de fondos el 100% del tiempo. ¿Cuál es el enfoque correcto?"

Respuesta: **Hook de intercepción de llamada de herramienta** (pre-ejecución) que bloquea `transfer_funds` hasta que `aml_check` devuelva un resultado de pase verificado.

Respuestas incorrectas:
- Instrucciones de prompts (probabilístico, ~90-95%)
- Hooks PostToolUse (se ejecutan después — no pueden bloquear)

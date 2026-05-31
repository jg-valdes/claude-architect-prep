# Coordinación de Subagentes

**Peso en el examen:** Dominio 1 – Arquitectura Agéntica y Orquestación (27%)

---

## La Herramienta Agent

La **herramienta Agent** (anteriormente llamada "Task") es el mecanismo de API para instanciar subagentes. El nombre anterior "Task" sigue siendo funcional como alias de retrocompatibilidad.

**Requisito obligatorio:** El `allowedTools` de un coordinador debe incluir explícitamente `"Agent"` o `"Task"` para instanciar subagentes. Sin esto, el coordinador no puede invocar ningún subagente, independientemente de otra configuración.

Cada subagente se define mediante un **AgentDefinition** que especifica:
1. Descripción (qué hace el subagente)
2. Prompt del sistema (sus instrucciones)
3. Restricciones de herramientas (a qué herramientas puede acceder)

---

## Tres Reglas para Pasar Contexto

**Regla 1: Pasar hallazgos completos en su totalidad.**
Los agentes posteriores no pueden acceder retroactivamente a resultados anteriores — toda la información necesaria debe incluirse en su prompt.

**Regla 2: Usar formatos estructurados que separen contenido de metadatos.**
Los hallazgos deben incluir tanto afirmaciones como información de fuente (URLs, nombres de documentos, números de página). Sin metadatos, los agentes posteriores no pueden atribuir afirmaciones a fuentes.

**Regla 3: Diseñar prompts del coordinador en torno a objetivos, no procedimientos.**
Los prompts orientados a objetivos permiten adaptabilidad; las instrucciones de procedimiento limitan la flexibilidad del subagente.

---

## Formato de Metadatos Estructurados

```json
{
  "hallazgos": [
    {
      "afirmacion": "La eficiencia de paneles solares aumentó 25% en la última década",
      "url_fuente": "https://ejemplo.com/informe-solar",
      "nombre_documento": "Informe Anual de la Industria Solar 2024",
      "numero_pagina": 14,
      "confianza": "alta",
      "recuperado_por": "agente_busqueda_web"
    }
  ]
}
```

Cada hallazgo lleva **metadatos completos de atribución de fuente**.

---

## Instanciación en Paralelo

Cuando múltiples subagentes realizan tareas independientes, emitir **múltiples llamadas a la herramienta Agent en una sola respuesta del coordinador** en lugar de invocarlos secuencialmente en turnos separados.

- Reduce la latencia para trabajo independiente
- El examen evalúa específicamente la conciencia de latencia — buscar respuestas que mencionen "simultáneamente" o "en una sola respuesta"

---

## `fork_session` vs. `--resume`

| Mecanismo | Propósito |
|---|---|
| `fork_session` | Crea ramas independientes desde una línea base de análisis compartida para **exploración divergente** |
| `--resume` | Continúa una sesión nombrada específica en el **mismo camino de investigación** |

Sirven propósitos diferentes y se **confunden frecuentemente en exámenes**.

---

## Puntos Clave del Examen

- `allowedTools` debe incluir `"Agent"` o `"Task"` — requisito binario obligatorio
- Los subagentes tienen **contexto aislado** — solo reciben información pasada explícitamente
- Citas faltantes = el coordinador pasó contenido **sin metadatos estructurados**
- Las tareas independientes deben instanciarse **en paralelo** (respuesta única), no secuencialmente
- `fork_session` ≠ `--resume` — conocer la diferencia

---

## Trampa Común del Examen

> Un agente de síntesis produce afirmaciones sin fuente mientras los subagentes de búsqueda web y análisis de documentos funcionan correctamente. ¿Cuál es la causa raíz?

**Respuesta:** El coordinador pasa contenido al agente de síntesis sin metadatos estructurados — las URLs de fuente, nombres de documentos y números de página no están incluidos. El agente de síntesis no puede citar fuentes que nunca recibió.

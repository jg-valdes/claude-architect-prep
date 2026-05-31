# Distribución de Herramientas y Elección de Herramienta

**Peso en el examen:** Dominio 2 – Diseño de Herramientas e Integración MCP (18%)

---

## El Problema de Sobrecarga de Herramientas

El rango óptimo es **4–5 herramientas por agente**, con alcance al rol específico de ese agente.

Dar a un solo agente demasiadas herramientas degrada la fiabilidad de selección e incrementa las tasas de error. Las herramientas deben coincidir con la especialización del agente — un agente de síntesis no debería tener capacidades de búsqueda web, y viceversa.

---

## Tres Modos de Configuración `tool_choice`

| Modo | Comportamiento | Garantía de Salida |
|---|---|---|
| `"auto"` (predeterminado) | El modelo decide si llamar una herramienta o responder conversacionalmente | Ninguna — puede responder con texto |
| `"any"` | El modelo DEBE llamar alguna herramienta pero elige cuál | Salida estructurada garantizada |
| Nombre específico de herramienta | El modelo DEBE llamar esa herramienta exacta nombrada | Control máximo, paso obligatorio |

**Crítico:** `"auto"` NO garantiza salida estructurada. Usar `"any"` o nombre de herramienta específico cuando se requiere estructura.

---

## Herramientas de Rol Cruzado con Alcance

En lugar de enrutar todas las solicitudes a través de un coordinador (añadiendo 2–3 viajes de ida y vuelta), los agentes pueden tener versiones restringidas de capacidades necesarias.

**Ejemplo:** Dar a un agente de síntesis una herramienta `verify_fact` limitada para búsquedas simples mientras se enrutan verificaciones complejas al coordinador. Evita latencia innecesaria.

---

## Diseño de Herramientas de Mínimo Privilegio

Reemplazar herramientas genéricas con alternativas restringidas:
- Genérica: `fetch_url` (puede obtener cualquier cosa)
- Restringida: `load_document` (valida solo URLs de documentos)

Esto previene el mal uso y aclara el propósito de la herramienta.

---

## Ejemplo de Distribución por Rol

| Agente | Herramientas (4–5 cada uno) |
|---|---|
| Búsqueda Web | `search_web`, `fetch_page`, `extract_links`, `save_snippet` |
| Análisis de Documentos | `extract_metadata`, `extract_data_points`, `summarize_content`, `verify_claim` |
| Síntesis | `compile_report`, `verify_fact` (con alcance), `format_citation`, `assess_coverage` |
| Coordinador | `Agent`, `review_output`, `request_revision` |

---

## Transporte stdio vs. SSE

| Transporte | Caso de Uso | Autenticación |
|---|---|---|
| `stdio` | Servidores locales, procesos en la misma máquina | No se necesita auth (aislamiento de proceso) |
| `SSE` | Servidores remotos, configuraciones multi-cliente | Requiere autenticación (expuesto en red) |

Seleccionar `stdio` para herramientas de desarrollo local. Seleccionar `SSE` cuando el servidor MCP se ejecuta en un host remoto o necesita servir a múltiples clientes simultáneamente.

---

## Puntos Clave del Examen

- Conteo óptimo de herramientas por agente: **4–5**, con alcance al rol
- `"auto"` NO garantiza salida estructurada — usar `"any"` o nombre específico de herramienta
- Mínimo privilegio: preferir herramientas restringidas sobre genéricas
- Las herramientas de rol cruzado con alcance reducen los viajes de ida y vuelta del coordinador
- `stdio` = local, `SSE` = remoto (requiere auth)

---

## Trampas Comunes del Examen

- Usar `tool_choice: "auto"` cuando se requiere salida estructurada
- Asignar 15+ herramientas esperando selección confiable
- Herramientas genéricas que permiten comportamientos no deseados del agente
- Enrutar búsquedas simples a través de coordinadores innecesariamente

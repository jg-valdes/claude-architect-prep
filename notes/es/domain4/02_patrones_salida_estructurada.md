# Patrones de Salida Estructurada

**Peso en el examen:** Dominio 4 – Ingeniería de Prompts y Salida Estructurada (20%)

---

## Jerarquía de Fiabilidad

De más a menos confiable para salida estructurada:

1. **`tool_use` con esquemas JSON** — elimina errores de sintaxis
2. **JSON basado en prompts** — sin garantías estructurales

---

## Modos del Parámetro `tool_choice`

| Modo | Comportamiento | Garantía de Salida |
|---|---|---|
| `"auto"` | El modelo decide: herramienta o conversacional | Ninguna — puede responder con texto |
| `"any"` | El modelo DEBE llamar alguna herramienta, elige cuál | Salida estructurada garantizada |
| Nombre específico de herramienta | El modelo DEBE llamar esa herramienta exacta | Control máximo, paso obligatorio |

**Crítico:** `"auto"` NO garantiza salida estructurada. Usar `"any"` o nombre de herramienta específico cuando se requiere estructura.

---

## Qué Previene y NO Previene `tool_use` con Esquemas

| Previene | NO previene |
|---|---|
| JSON malformado | Valores incorrectos en campos correctos |
| Campos requeridos faltantes | Discrepancias en sumas |
| Incompatibilidades de tipo | Datos mal colocados |
| — | Valores fabricados/alucinados |

> `tool_use` con esquemas JSON elimina errores de **sintaxis**. NO previene errores **semánticos**.

---

## Mejores Prácticas de Diseño de Esquema

### Campos Opcionales/Anulables

Hacer campos opcionales o anulables cuando los documentos fuente pueden carecer de información:

```json
{
  "fecha_factura": { "type": "string", "nullable": true },
  "monto_impuesto": { "type": "number", "nullable": true }
}
```

**Por qué:** Los campos requeridos presionan al modelo a inventar valores plausibles (alucinación). Los campos opcionales permiten respuestas `null` honestas.

### Valores Enum para Ambigüedad

```json
"confianza": { "enum": ["alta", "media", "baja", "incierta"] }
```

Usar `"incierta"` como valor enum válido en lugar de forzar una elección.

### Manejo de Casos Extremos

```json
"categoria": { "type": "string", "enum": ["facturacion", "tecnico", "otro"] },
"detalle_categoria": { "type": "string", "description": "Requerido cuando categoría es 'otro'" }
```

---

## Arquitectura de Revisión Multi-Instancia

Para revisiones grandes que sufren de dilución de atención:

1. **Pasada 1 — Análisis local por archivo:** Cada archivo analizado individualmente con presupuesto completo de atención
2. **Pasada 2 — Integración entre archivos:** Análisis separado verificando flujo de datos, contratos de API, contradicciones

**Por qué las ventanas de contexto más grandes no ayudan:** "Una ventana de contexto más grande no previene que el modelo dé atención desigual a los archivos." El problema es la calidad de la atención, no la capacidad.

---

## Enrutamiento Basado en Confianza

Los hallazgos pueden incluir puntuaciones de confianza (0.0–1.0) que habilitan enrutamiento estratégico:
- Alta confianza → reporte directo al desarrollador
- Baja confianza → cola de revisión humana

**Anti-patrón crítico:** "Usar confianza no calibrada para decisiones automatizadas." Los umbrales deben calibrarse usando conjuntos de validación etiquetados.

---

## Puntos Clave del Examen

- `"any"` garantiza una llamada a herramienta; `"auto"` no lo hace
- `tool_use` previene errores de sintaxis, no errores semánticos
- Campos requeridos = riesgo de alucinación; opcional/anulable = más seguro
- Auto-revisión (misma sesión) < revisión de instancia independiente
- El tamaño de la ventana de contexto no soluciona la dilución de atención
- Calibrar puntuaciones de confianza antes de usarlas para enrutamiento

---

## Trampas Comunes del Examen

- Asumir que `tool_use` previene todos los errores (solo sintaxis)
- Confundir `"auto"` (sin garantía) con `"any"` (llamada a herramienta garantizada)
- Hacer todos los campos del esquema requeridos
- Usar puntuaciones de confianza brutas no calibradas para enrutamiento automatizado
- Recomendar ventana de contexto más grande para solucionar dilución de atención

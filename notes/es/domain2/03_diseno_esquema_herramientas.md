# Diseño de Esquema de Herramientas

**Peso en el examen:** Dominio 2 – Diseño de Herramientas e Integración MCP (18%)

---

## Por Qué las Descripciones Son Críticas

**Las descripciones de herramientas son el mecanismo primario que usan los LLMs para la selección de herramientas.**

No son metadatos opcionales — determinan directamente si los modelos enrutan las consultas correctamente. Una descripción vaga como "Recupera información del cliente" crea ambigüedad que lleva al enrutamiento incorrecto.

---

## Cinco Elementos Esenciales

Las descripciones de herramientas de nivel producción deben incluir:

1. **Propósito principal** — declaración inequívoca de qué hace la herramienta
2. **Especificaciones de entrada** — tipos de datos, formatos, restricciones, campos requeridos vs. opcionales
3. **Casos de uso concretos** — consultas de ejemplo que muestran el alcance previsto de la herramienta
4. **Limitaciones y casos extremos** — límites explícitos y modos de fallo
5. **Diferenciación de herramientas** — declaraciones claras sobre cuándo usar ESTA herramienta vs. alternativas similares

---

## El Problema de Enrutamiento Incorrecto

Las descripciones mínimas crean ambigüedad cuando múltiples herramientas tienen propósitos superpuestos.

### Jerarquía de Resolución de Problemas (bajo → alto esfuerzo)

1. ✅ **Ampliar descripciones** (primer paso correcto)
2. ✗ Ejemplos few-shot (agrega sobrecarga sin solucionar la causa raíz)
3. ✗ Clasificadores de enrutamiento (sobreingeniería)
4. ✗ Consolidación de herramientas (válido a largo plazo pero prematuro)

**Siempre mejorar las descripciones antes de intentar cambios de infraestructura.**

---

## Estrategia de División de Herramientas

Las herramientas genéricas deben dividirse en herramientas específicas de propósito:

```
analyze_document  →  extract_document_data
                     summarize_document
                     verify_document_claims
```

Cada herramienta enfocada obtiene una descripción clara que previene el enrutamiento incorrecto.

---

## Mejores Prácticas de Diseño de Esquema

- Hacer campos **opcionales/anulables** cuando los documentos fuente pueden carecer de información — previene alucinaciones al permitir respuestas `null` honestas
- Usar **valores enum** como `"unclear"` para casos ambiguos
- Proporcionar una cadena `"other"` + detalle para casos extremos
- Incluir instrucciones de normalización de formato en los prompts

---

## Consejo del Examen: Verificar Conflictos en el Prompt del Sistema

Los prompts del sistema pueden contener palabras clave que anulan las descripciones de herramientas bien escritas. Siempre auditar los prompts del sistema en busca de conflictos cuando la selección de herramientas es poco confiable.

---

## Puntos Clave del Examen

- Las descripciones de herramientas = mecanismo de selección **primario**, no metadatos opcionales
- **Mejorar las descripciones primero** antes de agregar infraestructura de enrutamiento
- División de herramientas: una herramienta, una responsabilidad clara
- Los campos opcionales/anulables previenen alucinaciones en datos faltantes
- Los conflictos en el prompt del sistema pueden anular buenas descripciones — verificar ambos

---

## Trampas Comunes del Examen

- Elegir ejemplos few-shot o clasificadores de enrutamiento como primera solución para el enrutamiento incorrecto
- Asumir que las descripciones bien escritas son suficientes sin verificar conflictos en el prompt del sistema
- Mantener herramientas genéricas esperando que las descripciones manejen todos los casos
- Hacer todos los campos del esquema requeridos, lo que presiona al modelo a alucinar valores

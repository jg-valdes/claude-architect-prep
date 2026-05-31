# Manejo de Errores de Herramientas

**Peso en el examen:** Dominio 2 – Diseño de Herramientas e Integración MCP (18%)

---

## Cuatro Elementos Requeridos de Contexto de Error

Cuando un error de subagente se propaga al coordinador, debe incluir:

1. **Tipo de fallo** — transitorio, validación, negocio o permiso
2. **Acción intentada** — la herramienta, consulta y parámetros utilizados
3. **Resultados parciales** — datos recuperados exitosamente antes del fallo (no descartar)
4. **Enfoques alternativos** — sugerencias del conocimiento del dominio del subagente

---

## Categorías de Error

| Categoría | `isRetryable` | Estrategia de Recuperación |
|---|---|---|
| Transitorio (timeout, límite de tasa) | `true` | Reintentar con retroceso exponencial |
| Validación (formato incorrecto, campo faltante) | `true` | Corregir entrada y reintentar |
| Negocio (violación de política) | `false` | Escalar o flujo alternativo |
| Permiso (acceso denegado) | `false` | Escalar u obtener credenciales diferentes |

---

## Fallo de Acceso vs. Resultado Vacío Válido

Esta distinción se **evalúa intensamente**:

| Situación | `isError` | Acción Correcta |
|---|---|---|
| La herramienta no pudo alcanzar la fuente (timeout, error de conexión) | `true` | Considerar reintento |
| La herramienta se ejecutó exitosamente, no encontró registros | `false` | Sin reintento — esta ES la respuesta |

**Nunca reintentar un resultado vacío válido.** Una consulta exitosa con cero resultados significa que los datos no existen, no que la herramienta falló.

---

## Dos Anti-Patrones Críticos

### Supresión Silenciosa
Devolver resultados vacíos marcados como exitosos cuando ocurrió un timeout. Esto previene toda recuperación porque el coordinador cree que la búsqueda tuvo éxito y no encontró nada.

### Terminación del Flujo
Terminar el pipeline completo cuando falla un subagente, descartando todo el trabajo de los subagentes completados exitosamente.

---

## Propagación de Errores Multi-Agente

Mejores prácticas:
- Los subagentes realizan **reintento local** para fallos transitorios antes de escalar
- Solo propagar errores que no puedan resolverse localmente
- Las salidas de síntesis deben incluir **anotaciones de cobertura** indicando temas bien soportados vs. lagunas
- El contexto de error estructurado permite a los coordinadores tomar decisiones de recuperación inteligentes

---

## Puntos Clave del Examen

- Los errores de negocio **nunca son reintentables** — reintentar produce fallos idénticos
- Resultado vacío válido ≠ fallo de acceso — conocer la diferencia
- La supresión silenciosa es **peor** que propagar un error (bloquea la recuperación)
- Siempre propagar resultados parciales junto al contexto de error
- Reintento local antes de escalar — no inundar al coordinador con fallos transitorios

---

## Trampa Común del Examen

> "Una búsqueda en base de datos devuelve un array vacío con `isError: false`. ¿Debería el coordinador reintentar?"

Respuesta: No — `isError: false` significa que la herramienta se ejecutó exitosamente y no encontró registros. Reintentar desperdicia recursos y produce el mismo resultado.

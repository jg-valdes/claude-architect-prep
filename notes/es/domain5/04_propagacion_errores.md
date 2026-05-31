# Propagación de Errores en Sistemas Multi-Agente

**Peso en el examen:** Dominio 5 – Gestión de Contexto y Fiabilidad (15%)

---

## Cuatro Elementos Requeridos de Contexto de Error

Cuando un error de subagente se propaga al coordinador, debe incluir:

1. **Tipo de fallo** — transitorio / validación / negocio / permiso
2. **Acción intentada** — herramienta específica, consulta y parámetros utilizados
3. **Resultados parciales** — datos recuperados exitosamente antes del fallo (no descartar)
4. **Enfoques alternativos** — sugerencias del conocimiento del dominio del subagente

---

## Dos Anti-Patrones Críticos

### Supresión Silenciosa
Devolver resultados vacíos marcados como exitosos cuando ocurrió un timeout.

**Por qué es dañino:** Previene toda recuperación — el coordinador cree que la búsqueda tuvo éxito y no encontró nada. Sin reintento, sin enfoque alternativo.

### Terminación del Flujo
Terminar el pipeline completo cuando falla un subagente.

**Por qué es dañino:** Descarta todo el trabajo de los subagentes completados exitosamente.

---

## Fallo de Acceso vs. Resultado Vacío Válido

| Situación | Clasificación Correcta | Acción |
|---|---|---|
| La herramienta no pudo alcanzar la fuente (timeout, error de conexión) | Fallo de acceso — `isError: true` | Reintento local; escalar si persiste |
| La herramienta ejecutada exitosamente, no encontró registros | Resultado vacío válido — `isError: false` | Sin reintento — esta ES la respuesta |

---

## Mejores Prácticas

- Los subagentes realizan **reintento local** para fallos transitorios antes de escalar
- Solo propagar errores que no puedan resolverse localmente
- Las salidas de síntesis deben incluir **anotaciones de cobertura** indicando áreas bien soportadas vs. lagunas
- El contexto de error estructurado permite a los coordinadores elegir estrategias de recuperación inteligentes

---

## Puntos Clave del Examen

- Los 4 elementos de contexto de error son requeridos en los errores propagados
- La supresión silenciosa es **peor** que propagar un error
- La terminación del pipeline en fallo parcial pierde trabajo completado valioso
- Reintento local primero, escalar segundo
- `isError: false` con resultados vacíos = no reintentar

---

## Trampa Común del Examen

> "Un subagente de búsqueda web agota el tiempo. Devuelve `{ resultados: [], isError: false }`. El coordinador no hace nada y reporta ningún hallazgo. ¿Qué salió mal?"

Respuesta: El subagente usó **supresión silenciosa** — devolvió un éxito vacío en lugar de un error. El coordinador no tenía información para disparar la recuperación. Solución: devolver `isError: true` con tipo de fallo, acción intentada y resultados parciales.

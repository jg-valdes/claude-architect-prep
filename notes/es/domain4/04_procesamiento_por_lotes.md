# Procesamiento por Lotes y Optimización de Prompts

**Peso en el examen:** Dominio 4 – Ingeniería de Prompts y Salida Estructurada (20%)

---

## API de Lotes de Mensajes

**Ahorro de costos:** 50% comparado con llamadas a API síncronas.

**Limitación:** Ventana de procesamiento de hasta **24 horas** sin SLA de latencia garantizado.

**Limitación crítica:** No puede soportar llamadas a herramientas multi-turno dentro de una sola solicitud.

---

## Regla de Decisión

> Mantener los flujos de trabajo bloqueantes síncronos. Usar lotes solo para flujos de trabajo tolerantes a latencia.

| Tipo de Flujo de Trabajo | API | Por Qué |
|---|---|---|
| Verificaciones pre-merge (desarrolladores esperan) | Síncrona | Bloqueante — los usuarios necesitan resultados ahora |
| Revisiones de código en tiempo real | Síncrona | Bloqueante |
| Informes nocturnos | API de Lotes | Tolerante a latencia |
| Ejecuciones de auditoría semanales | API de Lotes | Tolerante a latencia |
| Flujos de trabajo agénticos con llamadas a herramientas | Síncrona | Los lotes no soportan llamadas a herramientas |

**El examen presenta a un gerente proponiendo cambiar todo a lotes por el ahorro de costos. La respuesta correcta mantiene los flujos de trabajo bloqueantes síncronos.**

---

## Cálculo de SLA

Trabajando hacia atrás desde los plazos:

**Ejemplo:** SLA de 30 horas
- 24 horas de procesamiento máximo de lotes
- = 6 horas de margen
- → Enviar lotes cada 4–6 horas para garantizar la finalización

---

## Manejo de Fallos (Patrón de Tres Pasos)

1. Identificar fallos mediante campos únicos `custom_id`
2. Reenviar solo los fallos con modificaciones (tokens aumentados, fragmentación de documentos)
3. Pre-refinar prompts en 5–10 documentos representativos antes del envío completo del lote

---

## Optimización de Costos

El refinamiento previo de prompts reduce drásticamente los costos de reintento.

| Tasa de éxito en primera pasada | Costo relativo de reenvío |
|---|---|
| 90% | Bajo |
| 60% | ~4 veces mayor |

Probar en un conjunto de muestra pequeño antes de ejecutar el lote completo.

---

## Puntos Clave del Examen

- API de Lotes = 50% más barata, hasta 24h de latencia, **sin llamadas a herramientas**
- Los flujos de trabajo bloqueantes (pre-merge) deben usar API **síncrona**
- El campo `custom_id` habilita reenvío de fallos parciales
- Pre-probar la calidad del prompt en 5–10 muestras antes de ejecuciones de lotes grandes
- Calcular margen del SLA: plazo menos 24h = tiempo máximo de envío

---

## Trampas Comunes del Examen

- Proponer API de lotes para flujos de trabajo bloqueantes pre-merge (incorrecto — los desarrolladores están esperando)
- Olvidar que la API de lotes no puede soportar llamadas a herramientas
- Ignorar el mecanismo `custom_id` para reintentos parciales
- Omitir el refinamiento de muestras antes de ejecuciones de lotes grandes

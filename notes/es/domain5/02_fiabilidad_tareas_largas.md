# Fiabilidad en Tareas de Larga Duración

**Peso en el examen:** Dominio 5 – Gestión de Contexto y Fiabilidad (15%)

---

## El Problema con las Tareas Largas

Las tareas cortas fallan rara vez y se recuperan fácilmente. Las tareas agénticas de larga duración enfrentan riesgos compuestos:
- Timeouts de red a mitad de la tarea
- Llenado de la ventana de contexto
- Errores en el paso 3 que corrompen los pasos 4–20
- El modelo desviándose del objetivo original
- Límites de recursos (límites de tasa, presupuestos de tokens)

Un buen diseño anticipa estos fallos y los maneja con gracia.

---

## Patrón 1: Checkpointing

Guarda el estado después de cada paso significativo para poder reanudar sin empezar de cero.

```
Tarea: Procesar 1,000 registros

Paso 1: Procesar registros 1–100   → guardar progreso: {"ultimo_procesado": 100}
Paso 2: Procesar registros 101–200 → guardar progreso: {"ultimo_procesado": 200}
...
Paso 6: Procesar registros 501–600 → FALLO

Reanudar: leer checkpoint → ultimo_procesado = 500 → comenzar desde 501
```

**Qué guardar en checkpoints:**
- Qué elementos se han procesado
- Resultados intermedios que costaron esfuerzo producir
- Fase/etapa actual del flujo de trabajo

**Dónde almacenar checkpoints:**
- Archivos (simple, duradero)
- Bases de datos (consultable, estructurado)
- Almacenes clave-valor (rápido, ligero)

---

## Patrón 2: Operaciones Idempotentes

Diseña cada paso para que ejecutarlo dos veces produzca el mismo resultado que ejecutarlo una vez.

```
Mal (no idempotente):
  "Añadir fila a la tabla" → ejecutar dos veces crea filas duplicadas

Bien (idempotente):
  "Insertar o actualizar fila por ID" → ejecutar dos veces produce la misma fila única
```

Las operaciones idempotentes hacen que los reintentos sean seguros — siempre puedes reintentar sin preocuparte por efectos secundarios.

---

## Patrón 3: Seguimiento de Progreso

Mantén un registro visible de qué se ha hecho, qué está en progreso y qué queda.

```json
{
  "total": 1000,
  "completados": 547,
  "en_progreso": 12,
  "fallidos": 3,
  "elementos_fallidos": [42, 156, 389],
  "restantes": 438
}
```

El seguimiento de progreso permite:
- Reanudar desde completación parcial
- Identificar patrones en los fallos
- Estimar el tiempo restante
- Reportar estado a los humanos

---

## Patrón 4: Degradación Elegante

Cuando una subtarea falla, decide si la tarea general puede continuar sin ella.

```
Ruta crítica:     Debe tener éxito — abortar si falla
Paso opcional:    Se puede omitir — continuar con salida reducida
Paso de mejor esfuerzo: Intentar 3 veces — si todos fallan, registrar y continuar
```

No todo fallo debe abortar la tarea completa. Diseña políticas explícitas por tipo de paso.

---

## Patrón 5: Timeouts y Plazos

Siempre establece timeouts en operaciones individuales y duración total de la tarea.

```
Timeout por llamada a herramienta: 30 segundos
Timeout por subagente:             5 minutos
Timeout total de tarea:            2 horas → escalar a humano si se excede
```

Sin timeouts, una sola operación bloqueada detiene todo el flujo de trabajo indefinidamente.

---

## Manejo del Crecimiento del Contexto en Tareas Largas

A medida que una tarea larga avanza, la ventana de contexto se llena con historial. Estrategias:

1. **Resumen periódico** — cada N pasos, resume lo que se ha hecho y reemplaza el historial crudo
2. **Ventana deslizante** — mantén solo los últimos N pasos en contexto; los pasos más antiguos se guardan externamente
3. **Subagentes frescos** — para cada fase principal, lanza un nuevo subagente con solo el contexto que necesita (evita la acumulación de contexto por completo)

---

## Puntos Clave para el Examen

- El **checkpointing** es obligatorio para tareas largas — diséñalo desde el inicio, no como reflexión tardía
- Las **operaciones idempotentes** hacen que los reintentos sean seguros — siempre prefíerelas para pasos que pueden repetirse
- Los **timeouts** son necesarios en cada nivel — por operación, por fase y general
- La **degradación elegante** — clasifica cada paso como crítico, opcional o de mejor esfuerzo por adelantado
- El **crecimiento del contexto** en tareas largas debe gestionarse mediante resumen, ventanas deslizantes o subagentes frescos

---

## Trampa Común en el Examen

> "Una tarea agéntica de larga duración procesa 1,000 registros y falla en el registro 750. Sin ningún patrón de fiabilidad implementado, ¿cuál es la consecuencia?"

Respuesta: La tarea completa debe **reiniciarse desde el principio** — los 750 registros completados se pierden. El diseño correcto es el checkpointing después de cada lote para que la tarea reanude desde el registro 750, no desde el 1.

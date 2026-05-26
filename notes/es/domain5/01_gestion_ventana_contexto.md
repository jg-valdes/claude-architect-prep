# Gestión de la Ventana de Contexto

**Peso en el examen:** Dominio 5 – Gestión de Contexto y Fiabilidad (15%)

---

## ¿Qué Es la Ventana de Contexto?

La ventana de contexto es la cantidad total de texto (tokens) que un modelo puede procesar en una sola llamada — incluyendo el prompt del sistema, el historial de conversación, los resultados de herramientas y la respuesta que se está generando.

Cuando el contexto se llena:
- La información más antigua se trunca o se pierde
- El modelo pierde acceso a partes anteriores de la conversación
- El rendimiento se degrada a medida que la ventana se acerca a su límite

**Restricción clave:** Claude no puede acceder a información fuera de su ventana de contexto actual a menos que use una herramienta para recuperarla.

---

## Estrategias para Gestionar el Contexto

### 1. Resumen
En lugar de mantener el historial completo, resume el trabajo completado y reemplaza el historial crudo con el resumen.

```
Historial crudo (10,000 tokens)
  → Resumir en hallazgos clave (500 tokens)
  → Continuar con resumen + nuevo contexto
```

Usa cuando: conversaciones largas o tareas de múltiples pasos donde los detalles tempranos ya no son necesarios literalmente.

### 2. Memoria Externa
Almacena información fuera de la ventana de contexto y recupérala cuando sea necesario mediante herramientas.

```
Agente lee archivo A → extrae datos clave → guarda en almacén externo
Más tarde: agente consulta almacén externo → recupera solo lo necesario
```

Herramientas para memoria externa: lecturas de archivos, consultas de base de datos, búsqueda vectorial, almacenes clave-valor.

Usa cuando: la información total necesaria excede una ventana de contexto.

### 3. Fragmentación (Chunking)
Divide entradas grandes en piezas más pequeñas y procesa cada fragmento independientemente.

```
Documento grande (100 páginas)
  → Procesar páginas 1-10 → extraer hallazgos
  → Procesar páginas 11-20 → extraer hallazgos
  → ...
  → Agregar todos los hallazgos al final
```

Usa cuando: las entradas son demasiado grandes para caber en una sola ventana de contexto.

### 4. Carga Selectiva de Contexto
Solo carga el contexto relevante para la subtarea actual — no todo.

```
Mal:  Pasar todo el código base a cada subagente
Bien: Pasar solo los archivos relevantes para la tarea específica de cada subagente
```

Usa cuando: tienes más contexto disponible del que cualquier agente necesita.

### 5. Contexto Estructurado
Formatea el contexto para que la información más importante sea prominente y fácil de analizar.

```markdown
## Objetivo Actual
Corregir el bug de autenticación en src/auth.ts

## Archivos Relevantes
- src/auth.ts (el archivo con el bug)
- src/middleware/session.ts (dependencia)

## Lo que Hemos Probado
- Verificar lógica de expiración de token → no es el problema
- Verificar configuración de cookies → no es el problema

## Hipótesis Actual
El secreto JWT no se está cargando correctamente desde el entorno
```

---

## Presupuesto de Tokens

En flujos de trabajo agénticos de múltiples pasos, planifica tu presupuesto de tokens:

| Componente | Asignación típica |
|---|---|
| Prompt del sistema | 1,000–5,000 tokens |
| Contexto de la tarea | 5,000–20,000 tokens |
| Resultados de herramientas | Variable — resume si es grande |
| Respuesta | Reserva 2,000–4,000 tokens |

Si los resultados de herramientas son grandes (ej. un documento de 50 páginas), resume o fragmenta antes de incluir en el contexto.

---

## Puntos Clave para el Examen

- El **resumen** es la herramienta principal para gestionar el historial de conversación creciente
- La **memoria externa** (archivos, bases de datos) es la solución cuando la información total excede una ventana de contexto
- La **fragmentación** es el patrón para procesar entradas demasiado grandes para caber en una llamada
- La **carga selectiva de contexto** mantiene las llamadas a subagentes ligeras y enfocadas
- Los límites de la ventana de contexto son una **restricción dura** — diseña considerándolos

---

## Trampa Común en el Examen

> "Un sistema agéntico necesita procesar un documento de 500 páginas y extraer hechos clave. El documento es demasiado grande para una ventana de contexto. ¿Cuál es el enfoque correcto?"

Respuesta: **Fragmentación** — divide el documento en secciones, procesa cada sección independientemente para extraer hallazgos, luego agrega. No intentes meter todo el documento en una sola llamada.

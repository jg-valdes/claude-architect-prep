# Coordinación de Subagentes

**Peso en el examen:** Dominio 1 – Arquitectura Agéntica y Orquestación (27%)

---

## El Contrato Orquestador–Subagente

Cuando un orquestador lanza un subagente, debe proveer todo lo que el subagente necesita — los subagentes no tienen memoria compartida, no acceden al historial previo de conversación y no tienen conocimiento de otros subagentes.

**El orquestador es responsable de:**
- Proveer contexto completo en cada llamada a subagente
- Recopilar y almacenar resultados de subagentes
- Pasar resultados previos relevantes a subagentes posteriores
- Manejar fallos de subagentes y decidir si reintentar, omitir o abortar

**El subagente es responsable de:**
- Ejecutar su tarea específica
- Devolver un resultado en el formato esperado
- Reportar claramente el fallo si no puede completar la tarea

---

## Pasar Contexto Entre Subagentes

Dado que los subagentes son sin estado, el orquestador debe pasar explícitamente cualquier contexto que un subagente necesite:

```
# Mal — el subagente no sabe qué es "la investigación anterior"
Orquestador → Subagente B: "Escribe un resumen de la investigación anterior"

# Bien — el orquestador pasa los datos reales
Orquestador → Subagente B: "Escribe un resumen basándote en esta investigación: {resultado_investigacion}"
```

**Regla:** Nunca referenciar trabajo previo implícitamente. Siempre incluirlo explícitamente en el prompt.

---

## Coordinación Paralela vs Secuencial

### Secuencial (tareas dependientes)
Usar cuando la tarea B necesita la salida de la tarea A:
```
Orquestador → Subagente A → resultado A → Orquestador → Subagente B(resultado A) → resultado B
```

### Paralela (tareas independientes)
Usar cuando las tareas no dependen entre sí:
```
Orquestador → Subagente A ─┐
Orquestador → Subagente B ─┼→ Orquestador agrega todos los resultados
Orquestador → Subagente C ─┘
```

**Prefiere paralelo** donde las dependencias lo permitan — es más rápido y escala mejor.

---

## Validación de Resultados

Nunca confíes ciegamente en la salida de un subagente. El orquestador debe validar:

1. **Formato** — ¿está la salida en la estructura esperada (JSON, markdown, etc.)?
2. **Completitud** — ¿realizó el subagente todas las partes de la tarea?
3. **Corrección** — ¿tiene sentido la salida? (verificaciones puntuales, validación de esquema)

Si la validación falla:
- Reintentar con un prompt más claro
- Intentar un enfoque diferente
- Escalar a un humano

---

## Estrategias de Manejo de Errores

| Estrategia | Cuándo usar |
|---|---|
| **Reintentar** | Fallos transitorios (error de red, timeout) |
| **Reintentar con prompt ajustado** | El subagente malentendió la tarea |
| **Omitir y continuar** | La subtarea es opcional; el fallo es aceptable |
| **Abortar y reportar** | La subtarea es crítica; continuar sin ella es peligroso |
| **Escalar a humano** | El sistema no puede resolver el fallo automáticamente |

---

## Errores Comunes de Coordinación

| Error | Solución |
|---|---|
| Dar demasiado contexto a los subagentes | Pasar solo lo que el subagente necesita para su tarea específica |
| Asumir que los subagentes comparten estado | Siempre pasar estado explícitamente a través del orquestador |
| Lanzar subagentes para tareas triviales | Usar subagentes para trabajo que justifique la sobrecarga |
| Sin manejo de errores en resultados de subagentes | Siempre validar y manejar fallos |
| Tareas secuenciales que podrían ser paralelas | Mapear dependencias; paralelizar donde sea posible |

---

## Puntos Clave para el Examen

- **Los subagentes son completamente sin estado** — no saben nada excepto lo que el orquestador les dice en esta llamada
- **El orquestador es la única fuente de verdad** para el estado del sistema
- **La coordinación paralela** requiere tareas verdaderamente independientes — si hay cualquier dependencia, debe ser secuencial
- **Valida la salida del subagente** antes de pasarla al siguiente paso
- **El manejo de errores es responsabilidad del orquestador** — no del subagente

---

## Trampa Común en el Examen

> "Un subagente falla al completar su tarea debido a un timeout de red. ¿Qué debe hacer el orquestador?"

Respuesta: **Reintentar** el subagente (fallo transitorio). Si se agotan los reintentos, **abortar y reportar** — no continuar silenciosamente con datos incompletos ni reintentar infinitamente.

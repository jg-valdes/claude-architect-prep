# Patrones de Fiabilidad para Sistemas Agénticos

**Peso en el examen:** Dominio 1 – Arquitectura Agéntica y Orquestación (27%)

---

## Por Qué la Fiabilidad Es Difícil en Sistemas Agénticos

Los sistemas agénticos son no deterministas, de larga duración y toman acciones en el mundo real. Los fallos se componen: un paso malo puede corromper todos los pasos posteriores. A diferencia del software tradicional, no siempre puedes deshacer los cambios.

La fiabilidad debe diseñarse — no emerge naturalmente.

---

## Patrones Principales de Fiabilidad

### 1. Definir Criterios de Éxito por Adelantado
Antes de que cualquier agente tome acción, define qué significa "terminado" y "correcto".

```
Mal:  "Corrige el bug"
Bien: "El bug está corregido cuando `npm test` pasa con 0 fallos y el 
       error ya no aparece en los logs"
```

### 2. Checkpointing
Guarda resultados intermedios para que flujos de trabajo largos puedan reanudarse tras un fallo.

```
Paso 1 completo → guardar resultado en disco
Paso 2 completo → guardar resultado en disco
Paso 3 falla    → reanudar desde el resultado guardado del Paso 2
```

### 3. Puertas de Validación
Verifica la salida de cada paso antes de pasarla al siguiente.

```
Subagente produce salida
  → Validar formato (¿es JSON válido?)
  → Validar contenido (¿están presentes los campos requeridos?)
  → Validar corrección (¿pasa una verificación puntual?)
  → Solo entonces pasar al siguiente subagente
```

### 4. Reintento con Retroceso Exponencial
Para fallos transitorios (errores de red, límites de tasa), reintenta con retrasos crecientes:

```
Intento 1 falla → esperar 1s → reintentar
Intento 2 falla → esperar 2s → reintentar
Intento 3 falla → esperar 4s → reintentar
Intento 4 falla → escalar
```

Siempre limita los reintentos. Los reintentos infinitos pueden ocultar fallos reales.

### 5. Escalada al Humano
Para acciones de alto riesgo o irreversibles, pausa y obtén aprobación humana:

```
El agente planea eliminar 10,000 registros
  → Pausar
  → Mostrar al humano qué se eliminará
  → Esperar aprobación explícita
  → Entonces proceder
```

Disparadores clave para escalada humana:
- Acciones irreversibles (eliminar, publicar, enviar)
- Instrucciones ambiguas que podrían interpretarse de múltiples formas
- Fallos repetidos que el sistema no puede resolver
- Acciones fuera del alcance original

### 6. Mínima Huella
Limita el radio de impacto de los errores:

- Solicita **solo los permisos** necesarios para la tarea
- Toma **solo las acciones** requeridas para lograr el objetivo
- Prefiere **acciones reversibles** (mover vs eliminar, staging vs prod)
- Da **pasos pequeños** y valida antes de continuar

### 7. Verificación Independiente
Ten un agente separado revisando el trabajo del agente principal:

```
Agente Escritor produce salida
  → Agente Revisor la verifica independientemente
  → Las discrepancias se reportan al orquestador
```

---

## Anti-Patrones de Fiabilidad a Evitar

| Anti-patrón | Problema |
|---|---|
| Sin condiciones de parada | El bucle corre indefinidamente o sobrepasa el objetivo |
| Sin checkpointing | Se requiere reinicio completo tras cualquier fallo |
| Confiar en salida de subagente sin validación | Los datos incorrectos se propagan silenciosamente |
| Reintentos ilimitados | Oculta fallos reales, desperdicia recursos |
| Tomar acciones irreversibles sin confirmación | No se pueden deshacer los errores |
| Dar demasiados permisos a los agentes | Mayor radio de impacto cuando algo sale mal |

---

## Puntos Clave para el Examen

- **Define criterios de éxito antes de comenzar** — no después
- **El checkpointing** es la defensa principal contra fallos en tareas largas
- **Las puertas de validación** entre pasos previenen la propagación de errores
- **La escalada humana** es correcta para acciones irreversibles o ambiguas
- **La mínima huella** reduce el radio de impacto y es un principio de diseño explícito de Anthropic
- **La verificación independiente** es un patrón de fiabilidad, no solo de calidad

---

## Trampa Común en el Examen

> "Un sistema agéntico está a punto de enviar 50,000 correos a clientes. ¿Qué patrón de fiabilidad es más importante aplicar aquí?"

Respuesta: **Escalada al humano** — enviar correos es irreversible. El sistema debe pausar, mostrar exactamente qué se enviará y a quién, y requerir aprobación humana explícita antes de proceder.

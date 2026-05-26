# Descomposición de Tareas

**Peso en el examen:** Dominio 1 – Arquitectura Agéntica y Orquestación (27%)

---

## ¿Qué Es la Descomposición de Tareas?

La descomposición de tareas es cómo un orquestador divide un objetivo grande y complejo en subtareas más pequeñas y delimitadas que los subagentes pueden ejecutar de forma fiable.

Una buena descomposición es la diferencia entre un sistema que funciona y uno que se desvía, alucina o falla silenciosamente.

---

## Principios de una Buena Descomposición

### 1. Cada subtarea debe ser verificable independientemente
La salida de un subagente debe poder verificarse sin ejecutar todo el sistema.

### 2. Las subtareas deben tener entradas y salidas claras
Las tareas vagas producen resultados vagos. Define:
- Qué datos/contexto entran
- En qué formato debe estar el resultado
- Qué significa "terminado"

### 3. Minimizar dependencias entre subtareas
Las tareas que no dependen entre sí pueden ejecutarse en paralelo.

### 4. Mantener las subtareas dentro de una ventana de contexto
Si una subtarea requiere más contexto del que cabe en una ventana, necesita descomponerse más.

### 5. Fallar rápido — validar temprano
Coloca pasos de validación al inicio del pipeline. No ejecutes subagentes costosos con entrada incorrecta.

---

## Cómo Descomponer: Un Marco de Trabajo

Dado un objetivo complejo, pregunta:

1. **¿Cuáles son las fases distintas?** (investigar, escribir, revisar, formatear)
2. **¿Qué fases dependen entre sí?** (escribir depende de investigar; revisar depende de escribir)
3. **¿Qué fases pueden ejecutarse en paralelo?** (múltiples hilos de investigación)
4. **¿Qué necesita cada fase como entrada?** (datos específicos, no "todo")
5. **¿Qué produce cada fase?** (un artefacto específico)
6. **¿Cómo verificamos que cada fase tuvo éxito?** (verificación de esquema, revisión humana, prueba automatizada)

---

## Ejemplo: Sistema de Informe de Investigación

**Mala descomposición:**
```
Subagente 1: "Investiga el tema y escribe un informe"
```
Demasiado grande, no verificable, no se puede paralelizar.

**Buena descomposición:**
```
Subagente 1a: "Busca fuentes académicas sobre X"  → lista de URLs + resúmenes
Subagente 1b: "Busca fuentes de noticias sobre X" → lista de URLs + resúmenes
Subagente 1c: "Busca datos/estadísticas sobre X"  → lista de URLs + resúmenes
  (los tres ejecutan en paralelo)

Subagente 2: "Dado estas fuentes, extrae hallazgos clave" → JSON estructurado

Subagente 3: "Dado estos hallazgos, escribe un resumen de 500 palabras" → markdown

Subagente 4: "Revisa el resumen para verificar precisión contra las fuentes" → aprobación o correcciones
```

---

## Checkpoints

Para flujos de trabajo largos de múltiples pasos, guarda resultados intermedios para que los fallos no requieran empezar de nuevo:

- Después de que cada subagente importante complete, persiste su salida
- En caso de fallo, reanuda desde el último checkpoint exitoso
- Nunca asumas que una tarea agéntica larga se completará sin interrupciones

---

## Puntos Clave para el Examen

- **Descompón hasta que cada subtarea quepa en una ventana de contexto**
- **La ejecución paralela requiere subtareas independientes** — si A necesita la salida de B, son secuenciales
- **Define criterios de éxito por subtarea** antes de ejecutar — no después
- **El checkpointing** es esencial para la fiabilidad en flujos de trabajo largos
- El orquestador posee la lógica de descomposición — los subagentes solo ejecutan

---

## Trampa Común en el Examen

> "¿Qué enfoque de descomposición es más apropiado para una tarea de investigación compleja que requiere recopilar datos de múltiples fuentes simultáneamente?"

Respuesta: **Fan-out paralelo** — lanza múltiples subagentes simultáneamente para investigar diferentes fuentes, luego el orquestador agrega los resultados.

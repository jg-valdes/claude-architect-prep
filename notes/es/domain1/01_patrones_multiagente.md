# Patrones de Diseño de Sistemas Multi-Agente

**Peso en el examen:** Dominio 1 – Arquitectura Agéntica y Orquestación (27%)

---

## ¿Por Qué Sistemas Multi-Agente?

Una sola instancia de Claude tiene límites: una ventana de contexto, un hilo de ejecución, un conjunto de herramientas. Los sistemas multi-agente resuelven esto dividiendo el trabajo complejo entre agentes especializados que ejecutan en paralelo o en secuencia.

**Usa multi-agente cuando:**
- La tarea es demasiado grande para una ventana de contexto
- Las subtareas pueden ejecutarse en paralelo para ahorrar tiempo
- Diferentes subtareas necesitan herramientas o especializaciones distintas
- Necesitas verificación independiente (un agente verifica el trabajo de otro)

---

## Los Dos Roles Principales

### Orquestador
- Divide el objetivo general en subtareas
- Decide qué subagente maneja qué
- Recopila y sintetiza resultados
- Maneja errores y reintentos
- **No realiza el trabajo real** — dirige

### Subagente
- Recibe una tarea específica y delimitada
- La ejecuta usando sus herramientas
- Devuelve un resultado
- **No tiene memoria de turnos anteriores** — cada invocación es sin estado
- No necesita saber sobre otros subagentes

---

## Patrones de Diseño Clave

### 1. Hub-and-Spoke (más común)
Un orquestador, muchos subagentes especializados. El orquestador es el hub; los subagentes son los radios.

```
Orquestador
├── Agente de Investigación  → busca en la web
├── Agente Escritor          → redacta contenido
├── Agente Revisor           → verifica el borrador
└── Agente Formateador       → produce el formato final
```

Mejor para: flujos de trabajo con fases distintas, secuenciales o paralelas.

### 2. Pipeline (secuencial)
La salida de un agente alimenta al siguiente. Sin orquestador central.

```
Agente Ingesta → Agente Procesamiento → Agente Validación → Agente Salida
```

Mejor para: transformación de datos, tareas tipo ETL donde cada paso transforma la salida.

### 3. Fan-Out Paralelo
El orquestador envía la misma tarea a múltiples agentes simultáneamente, luego agrega resultados.

```
Orquestador
├── Agente A (analiza desde ángulo 1) ─┐
├── Agente B (analiza desde ángulo 2) ─┼→ Orquestador agrega
└── Agente C (analiza desde ángulo 3) ─┘
```

Mejor para: tareas de investigación, obtener múltiples perspectivas independientes, velocidad.

### 4. Jerárquico (orquestación anidada)
Un orquestador delega a sub-orquestadores, que a su vez gestionan sus propios subagentes.

```
Orquestador Principal
├── Sub-Orquestador A
│   ├── Agente A1
│   └── Agente A2
└── Sub-Orquestador B
    ├── Agente B1
    └── Agente B2
```

Mejor para: tareas muy grandes y complejas con múltiples flujos de trabajo distintos.

---

## Puntos Clave para el Examen

- **Los subagentes son sin estado** — no recuerdan interacciones previas; el orquestador debe pasar todo el contexto necesario en cada llamada
- **Los orquestadores sintetizan, los subagentes ejecutan** — nunca confundas los dos roles
- Multi-agente no siempre es mejor — para tareas simples, un agente es más rápido y económico
- **El paralelismo es un beneficio clave** — los agentes que no dependen entre sí deben ejecutarse simultáneamente
- La verificación independiente (un agente verifica la salida de otro) mejora la fiabilidad

---

## Trampa Común en el Examen

> "Un orquestador necesita pasar contexto del resultado de un subagente previo al siguiente. ¿Cómo funciona esto?"

Respuesta: El **orquestador** retiene los resultados e incluye explícitamente el contexto relevante en el prompt que envía al siguiente subagente. Los subagentes no comparten memoria — el orquestador es la única fuente de estado.

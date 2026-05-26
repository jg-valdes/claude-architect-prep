# Bucles Agénticos

**Peso en el examen:** Dominio 1 – Arquitectura Agéntica y Orquestación (27%)

---

## ¿Qué Es un Bucle Agéntico?

Un bucle agéntico es el patrón de ejecución principal de un agente autónomo: el agente toma una acción, observa el resultado, decide qué hacer a continuación, y repite — hasta que el objetivo se logra o se cumple una condición de parada.

```
┌─────────────────────────────────────────┐
│                                         │
│  Planificar → Actuar → Observar → Evaluar │
│                ↑                  ↓     │
│                └── bucle hasta terminar ┘
│                                         │
└─────────────────────────────────────────┘
```

---

## Las Cuatro Fases

### 1. Planificar
El agente decide qué acción tomar basándose en el objetivo y el estado actual.
- Debe referenciar el objetivo original
- Debe considerar lo que ya se ha hecho
- Debe elegir la acción mínima siguiente

### 2. Actuar
El agente llama a una herramienta (Bash, Read, Write, WebSearch, etc.).
- Una acción por iteración del bucle es más seguro que agrupar muchas
- Prefiere acciones reversibles — más fácil de recuperarse de errores

### 3. Observar
El agente lee la salida de la herramienta.
- ¿Tuvo éxito la acción?
- ¿Qué nueva información está disponible?
- ¿Ocurrió algo inesperado?

### 4. Evaluar
El agente decide: ¿se logró el objetivo? ¿Qué sigue?
- Si terminó → salir del bucle
- Si no terminó → volver a Planificar
- Si está atascado o fallando → escalar o abortar

---

## Condiciones de Parada (Crítico)

**Siempre define condiciones de parada antes de iniciar un bucle agéntico.** Sin ellas, los bucles corren indefinidamente, sobrepasan el objetivo o se quedan atascados.

Buenas condiciones de parada:
- ✅ "Detener cuando todas las pruebas pasen"
- ✅ "Detener después de procesar todos los elementos de la lista"
- ✅ "Detener si la misma acción falla 3 veces seguidas"
- ✅ "Detener después de un máximo de N iteraciones"

Malas condiciones de parada:
- ❌ "Detener cuando termine" (demasiado vago — ¿qué es terminar?)
- ❌ Sin condición de parada alguna

**Siempre incluye un conteo máximo de iteraciones** como red de seguridad.

---

## Modos de Fallo a Diseñar

### Bucles infinitos
El agente sigue tomando la misma acción que falla. Solución: rastrear acciones fallidas, no reintentar más de N veces.

### Deriva del objetivo
El agente se desvía del objetivo original a medida que avanza el bucle. Solución: releer el objetivo original al inicio de cada iteración.

### Errores compuestos
Un error temprano hace que todas las acciones posteriores sean incorrectas. Solución: valida resultados intermedios clave antes de continuar.

### Acciones demasiado amplias
El agente toma una acción grande e irreversible cuando una más pequeña bastaría. Solución: prefiere acciones mínimas y reversibles en cada paso.

---

## Principio de Mínima Huella

Este es un principio central de diseño de Anthropic para sistemas agénticos:

> **Solicita solo los permisos necesarios. Toma solo las acciones necesarias. Prefiere lo reversible sobre lo irreversible. Haz menos y confirma cuando haya incertidumbre.**

En la práctica:
- No uses `rm -rf` cuando puedes mover a un directorio temporal
- No escribas en producción cuando puedes escribir en un entorno de staging
- No proceses todos los registros cuando puedes procesar uno y verificar primero
- Pide aclaración antes de tomar una acción irreversible en una situación ambigua

---

## Puntos Clave para el Examen

- **Las condiciones de parada deben definirse antes de que inicie el bucle** — no como reflexión tardía
- **Siempre incluye un conteo máximo de iteraciones** como respaldo de seguridad
- El **principio de mínima huella**: prefiere acciones reversibles y de alcance mínimo
- Los **errores compuestos** son un riesgo importante — valida resultados intermedios
- Un agente que no puede progresar debe **escalar a un humano**, no hacer bucle indefinidamente

---

## Trampa Común en el Examen

> "Un bucle agéntico tiene la tarea de corregir pruebas fallidas. Después de 10 iteraciones, las pruebas siguen fallando. ¿Cuál es el mejor diseño para manejar esto?"

Respuesta: El bucle debe tener un **conteo máximo de iteraciones** con un comportamiento definido al alcanzarlo — mostrar el fallo al orquestador u operador humano. Continuar haciendo bucle no es una opción. Detenerse silenciosamente sin reportar tampoco.

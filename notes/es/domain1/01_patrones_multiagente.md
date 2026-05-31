# Patrones de Diseño de Sistemas Multi-Agente

**Peso en el examen:** Dominio 1 – Arquitectura Agéntica y Orquestación (27%)

---

## El Patrón Hub-and-Spoke

El examen se enfoca en un modelo específico: **hub-and-spoke con un coordinador central**. Este es el patrón evaluado.

```
         ┌──────────────┐
         │  Coordinador │  ← centro del hub
         └──────┬───────┘
      ┌─────────┼────��────┐
      ▼         ▼         ▼
  [Búsqueda] [Análisis] [Síntesis]  ← subagentes
  Agente     Agente      Agente
```

---

## Dos Roles

### Agente Coordinador
- Recibe la tarea inicial
- La divide en partes
- Selecciona qué subagentes activar
- Pasa contexto a los subagentes
- Recopila resultados y gestiona errores
- Enruta información entre subagentes

### Subagentes
- Trabajadores especializados en funciones específicas
- Reciben instrucciones **solo** del coordinador
- Devuelven resultados **exclusivamente** al coordinador

---

## La Regla Cardinal

> **TODA la comunicación fluye a través del coordinador. Los subagentes nunca se comunican directamente entre sí.**

Esto se aplica universalmente — no por eficiencia, no por conveniencia, nunca.

---

## Principio de Aislamiento Crítico

**Los subagentes NO tienen acceso automático a:**
- Las instrucciones del sistema del coordinador
- Mensajes anteriores del coordinador
- Las salidas de otros subagentes (salvo que se compartan explícitamente)
- Memoria compartida o estado global

**La memoria no persiste:** Dos invocaciones del mismo subagente son completamente independientes. La segunda no tiene conocimiento de la primera.

El coordinador debe incluir deliberadamente toda la información que cada subagente necesita en su prompt.

---

## Cuatro Responsabilidades del Coordinador

1. **Selección dinámica** — elegir qué subagentes invocar según los requisitos; no todas las consultas necesitan el pipeline completo
2. **Partición del alcance** — dividir temas para eliminar duplicación (un agente cubre fuentes académicas, otro cubre noticias)
3. **Refinamiento iterativo** — evaluar la salida en busca de lagunas y re-delegar con consultas dirigidas
4. **Enrutamiento centralizado** — toda la comunicación pasa por el coordinador

---

## El Patrón de Fallo por Descomposición Insuficiente

Un coordinador asignó "impacto de la IA en industrias creativas" pero solo abordó artes visuales, sin música, escritura ni cine.

- El agente de búsqueda encontró todo lo que se le asignó ✓
- El agente de síntesis combinó lo que recibi�� ✓
- Pero nada fue asignado nunca para los dominios faltantes ✗

**Causa raíz: la descomposición del coordinador, no la capacidad del subagente.**

Cuando la salida es incompleta en *alcance* (no en profundidad), rastrea los fallos hacia la descomposición del coordinador.

---

## Puntos Clave del Examen

- Hub-and-spoke es el patrón evaluado — memoriza los dos roles
- Los subagentes son **sin estado** y **aislados** — sin memoria compartida, sin contexto heredado
- Las lagunas de alcance son siempre culpa del **coordinador**, no del subagente
- La comunicación directa entre subagentes es **nunca** correcta
- Agregar más subagentes no soluciona problemas de descomposición

---

## Trampas Comunes del Examen

- Culpar a los subagentes por lagunas de cobertura que se originan en la descomposición del coordinador
- Asumir que los subagentes heredan el contexto del coordinador o comparten memoria
- Proponer comunicación directa entre subagentes como solución de eficiencia
- Agregar más subagentes para resolver problemas de descomposición

# Fiabilidad en Tareas Largas y Degradación de Contexto

**Peso en el examen:** Dominio 5 – Gestión de Contexto y Fiabilidad (15%)

---

## Degradación de Contexto (No Agotamiento de Tokens)

**Definición:** El modelo empieza a referenciar "patrones típicos" en lugar de las clases, métodos y cadenas de dependencias específicas que descubrió antes.

**Mecanismo:**
1. Cada paso de exploración produce salida verbosa
2. La salida se acumula en el contexto de conversación
3. Los descubrimientos anteriores quedan enterrados bajo la salida verbosa reciente
4. La atención del modelo se desplaza, perdiendo referencias específicas

> **La degradación de contexto no es un problema de límite de tokens. Aumentar la ventana de contexto no la soluciona.**

---

## Cuatro Estrategias de Mitigación

### 1. Archivos Borrador (Scratchpad)

Escribir hallazgos clave en archivos persistentes; referenciarlos para preguntas posteriores.

```markdown
# exploración-borrador.md
## Clases Clave
- OrderProcessor (src/orders/processor.ts) — maneja lógica de reembolsos
- PaymentGateway (src/payments/gateway.ts) — envuelve SDK de Stripe

## Cadena de Dependencias
OrderProcessor → PaymentGateway → StripeClient

## Hallazgos Críticos
- Verificación null faltante en línea 247 (posible NPE)
```

---

### 2. Delegación a Subagentes

En lugar de un agente explorando todo (llenando su contexto con salida verbosa), delegar tareas específicas a subagentes aislados:
- Encontrar archivos de prueba y estado de cobertura
- Rastrear flujos de reembolso a través de sistemas
- Identificar integraciones de API externas

> "Cada subagente opera con su propio contexto aislado... el contexto del agente principal permanece limpio para coordinación de alto nivel mientras los subagentes manejan la exploración verbosa."

**Comprensión clave:** El valor principal de los subagentes es el **aislamiento**, no la paralelización.

---

### 3. Inyección de Resumen Entre Fases

Cuando la exploración ocurre en fases, resumir los hallazgos de la Fase 1 **antes** de instanciar los subagentes de la Fase 2. Previene la duplicación y asegura que el contexto arquitectural se transfiera.

---

### 4. El Comando `/compact`

El comando `/compact` de Claude Code reduce el uso de contexto resumarizando conversaciones.

**Usar proactivamente** durante sesiones de exploración extendidas — no solo al alcanzar los límites de contexto.

---

## Recuperación de Fallos mediante Manifiestos de Estado

Para sesiones extendidas que pueden fallar o interrumpirse:

```json
{
  "id_sesion": "explorar-2024-03-15",
  "fase_actual": "fase_2_analisis_profundo",
  "rutas_exploradas": ["src/orders/", "src/payments/"],
  "hallazgos_clave": ["Verificación null faltante en processor.ts:247"],
  "preguntas_pendientes": ["¿PaymentGateway reintenta en 429?"]
}
```

En recuperación: inyectar el manifiesto como contexto, reanudar desde el último checkpoint.

---

## Puntos Clave del Examen

- Degradación de contexto = pérdida de atención por acumulación verbosa, **no** agotamiento de tokens
- Las ventanas de contexto más grandes **no** solucionan la degradación de contexto
- El valor de los subagentes = **aislamiento** (no paralelización) para contexto principal limpio
- Usar `/compact` proactivamente, no reactivamente
- Los archivos borrador + manifiestos de estado habilitan recuperación sin re-exploración completa

---

## Trampas Comunes del Examen

- Recomendar aumento de la ventana de contexto para degradación de contexto (es calidad de atención, no tamaño)
- Reiniciar la sesión sin guardar estado (pierde el conocimiento acumulado)
- Usar `/compact` solo como medida de emergencia en lugar de mantenimiento proactivo
- Pensar que la delegación a subagentes trata sobre velocidad en lugar de aislamiento de contexto

# Patrones de Fiabilidad para Sistemas Agénticos

**Peso en el examen:** Dominio 1 – Arquitectura Agéntica y Orquestación (27%)

---

## Aplicación de Flujos: Programática vs. Basada en Prompts

| Enfoque | Fiabilidad | Cuándo Usar |
|---|---|---|
| Guía basada en prompts | ~90–95% (probabilística) | Operaciones de bajo riesgo, preferencias de formato |
| Aplicación programática | 100% (determinista) | Operaciones financieras, controles de seguridad, cumplimiento |

**La regla de decisión:** Si un solo fallo causaría pérdida financiera, brecha de seguridad o violación de cumplimiento → usar aplicación programática.

---

## Puertas de Prerrequisito

Bloques a nivel de código que impiden la ejecución de herramientas hasta que se cumplan las precondiciones.

**Ejemplo:** `process_refund` no puede ejecutarse hasta que `get_customer` haya devuelto credenciales verificadas en la sesión actual.

Esto elimina la tasa de fallo documentada de ~8% de los enfoques solo con prompts.

---

## Disparadores de Intervención Humana

Disparadores válidos para escalar a un humano:

1. **Solicitud explícita del humano** — el cliente pide directamente hablar con una persona; cumplir inmediatamente sin intentar resolver primero
2. **Excepciones o lagunas de política** — la situación cae fuera de los límites de política documentados
3. **Incapacidad de avanzar** — errores de herramientas, acceso insuficiente al sistema o problemas técnicos que requieren intervención de ingeniería

**Anti-patrones (disparadores no confiables):**
- Escalada basada en sentimiento — el nivel de frustración no correlaciona con la complejidad del caso
- Puntajes de confianza auto-reportados — mal calibrados, los modelos muestran falsa confianza en casos difíciles

---

## Requisitos de Transferencia Estructurada

Los agentes humanos no tienen acceso al transcript de conversación, por lo que las transferencias deben ser **auto-contenidas** con cinco campos obligatorios:
1. ID del cliente
2. Resumen de conversación
3. Análisis de causa raíz
4. Monto de reembolso (si aplica)
5. Acción recomendada

---

## Checkpointing para Tareas Largas

Para operaciones agénticas extendidas:
- Guardar resultados intermedios en almacenamiento persistente en hitos clave
- Ante un fallo, reanudar desde el último checkpoint en lugar de reiniciar desde cero
- Incluir estado de sesión: fase actual, rutas exploradas, hallazgos clave, pasos pendientes

---

## Degradación Elegante

Cuando un subagente falla:
- **No suprimir silenciosamente** — devolver resultados vacíos marcados como exitosos; esto previene toda recuperación
- **No terminar el pipeline** — descartar trabajo de subagentes completados exitosamente
- **Sí propagar errores estructurados** con: tipo de fallo, acción intentada, resultados parciales, enfoques alternativos

---

## Puntos Clave del Examen

- Aplicación programática = **determinista**; prompts = **probabilístico**
- Usar puertas de prerrequisito para flujos financieros/seguridad/cumplimiento
- Clientes frustrados con problemas resolubles → resolver, no escalar
- Clientes tranquilos solicitando excepciones de política → escalar, no intentar resolver
- Las transferencias deben ser auto-contenidas — los humanos no tienen acceso al transcript
- La supresión silenciosa de errores y la terminación del pipeline son ambas **incorrectas**

---

## Trampa Común del Examen

> "Un requisito de cumplimiento exige que las verificaciones AML se completen antes de cualquier transferencia de fondos. ¿Cuál es el mecanismo de aplicación correcto?"

Respuesta: Un **hook de intercepción de llamada de herramienta** (pre-ejecución) que bloquea `transfer_funds` hasta que `aml_check` devuelva un resultado de pase verificado. Las instrucciones de prompts no pueden garantizar cumplimiento del 100% — solo la aplicación programática puede.

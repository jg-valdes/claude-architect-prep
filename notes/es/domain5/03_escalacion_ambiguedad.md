# Escalación y Resolución de Ambigüedad

**Peso en el examen:** Dominio 5 – Gestión de Contexto y Fiabilidad (15%)

---

## Tres Disparadores de Escalación Válidos

Exactamente tres situaciones justifican escalar a un humano:

1. **Solicitud explícita del humano** — el cliente pide directamente hablar con una persona; cumplir inmediatamente sin intentar resolver primero
2. **Excepciones o lagunas de política** — la situación cae fuera de los límites de política documentados; requiere juicio humano
3. **Incapacidad de avanzar** — errores de herramientas, acceso insuficiente al sistema o problemas técnicos que requieren intervención de ingeniería

---

## Dos Disparadores No Confiables (Anti-Patrones)

| Anti-Patrón | Por Qué Falla |
|---|---|
| Escalación basada en sentimiento | El nivel de frustración no correlaciona con la complejidad — un cliente enojado puede tener un problema simple resolubl |
| Puntajes de confianza auto-reportados | Los LLMs muestran falsa confianza en casos difíciles mientras son innecesariamente inciertos en casos fáciles |

---

## Matiz Clave: Clientes Frustrados

- Cliente frustrado + **problema resolubl** → reconocer la frustración, proporcionar solución (NO escalar)
- Cliente frustrado + **excepción de política necesaria** → escalar (disparador #2)
- Cliente reitera preferencia por humano después de ser ofrecido ayuda → escalar (disparador #1)

---

## Coincidencia Ambigua de Clientes

Cuando múltiples registros de clientes coinciden con una consulta de búsqueda:
- **Solicitar información de desambiguación** (correo electrónico, teléfono, número de pedido)
- **Nunca seleccionar heurísticamente** — previene violaciones de privacidad y acciones incorrectas

---

## Solución Recomendada para Mala Escalación

Agregar **criterios de escalación explícitos con ejemplos few-shot** directamente a los prompts del sistema — un enfoque proporcionado antes de cambios de infraestructura como modelos clasificadores o análisis de sentimiento.

---

## Puntos Clave del Examen

- Exactamente **3 disparadores de escalación válidos** — memorizarlos
- Sentimiento ≠ complejidad — no usar frustración como señal de escalación
- Los puntajes de confianza de LLM están mal calibrados — no usar como disparador principal
- Coincidencia ambigua de cliente → siempre desambiguar, nunca adivinar
- Solucionar mala escalación con criterios explícitos + ejemplos few-shot primero

---

## Trampa Común del Examen

> "Un agente escala cada vez que un cliente expresa frustración. ¿Cuál es la causa raíz?"

Respuesta: La escalación basada en sentimiento es poco confiable. Solución: agregar criterios de escalación categóricos explícitos con ejemplos few-shot que distingan casos frustrados resolubles de disparadores genuinos de escalación.

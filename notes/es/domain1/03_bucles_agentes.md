# Bucles Agénticos

**Peso en el examen:** Dominio 1 – Arquitectura Agéntica y Orquestación (27%)

---

## ¿Qué Es un Bucle Agéntico?

Un bucle agéntico es el **ciclo de ejecución principal** que impulsa a todo agente basado en Claude. Repite un patrón determinista de cuatro pasos hasta su finalización.

---

## El Ciclo de Cuatro Pasos

| Paso | Acción |
|------|--------|
| 1. Enviar solicitud | Llamar a la Messages API con el historial de conversación + resultados de herramientas previos |
| 2. Inspeccionar `stop_reason` | Verificar el campo de respuesta que indica qué sucede a continuación |
| 3. Manejar `tool_use` | Ejecutar las herramientas solicitadas, agregar resultados al historial de conversación |
| 4. Manejar `end_turn` | Presentar la respuesta final cuando Claude señala finalización |

---

## El Campo `stop_reason` — La Única Señal Confiable

`stop_reason` es **la única señal confiable para el control del bucle**. Dos valores importan:

- `"tool_use"` → continuar el bucle, ejecutar herramientas
- `"end_turn"` �� terminar el bucle, devolver respuesta

### Tres Anti-Patrones a Evitar

| Anti-Patrón | Por Qué Falla |
|---|---|
| Analizar lenguaje natural ("ya terminé") | Ambiguo — Claude puede usar frases similares a mitad de la tarea |
| Límites de iteración arbitrarios como parada principal | Desperdicia recursos, puede terminar trabajo válido anticipadamente |
| Verificar `response.content[0].type == "text"` | Falla — Claude devuelve texto *junto con* bloques `tool_use` simultáneamente |

---

## Guiado por el Modelo vs. Pre-Configurado

Claude **decide la selección de herramientas basándose en el contexto** (guiado por el modelo). Los árboles de decisión pre-configurados carecen de esta adaptabilidad.

**Excepción:** los requisitos de cumplimiento determinista anulan la flexibilidad del modelo — usa aplicación programática allí.

---

## Límites de Iteración de Seguridad

Aceptables **como salvaguardas de respaldo** contra bucles desbocados — nunca como mecanismo de terminación principal.

---

## Principio de Mínima Huella

Principio de diseño central de Anthropic:

> Solicita solo los permisos necesarios. Toma solo las acciones necesarias. Prefiere reversible sobre irreversible. Haz menos y confirma cuando tengas dudas.

En práctica:
- No usar `rm -rf` cuando puedes mover a un directorio temporal
- No procesar todos los registros cuando puedes procesar uno y verificar primero
- Solicitar aclaración antes de acciones irreversibles en situaciones ambiguas

---

## Puntos Clave del Examen

- `stop_reason` es la **única** señal confiable de control del bucle
- **Nunca** usar verificación de tipo de contenido o análisis de lenguaje natural como condiciones de parada
- Los límites de iteración de seguridad son **respaldos**, no mecanismos principales
- La selección de herramientas guiada por el modelo habilita flexibilidad; la aplicación programática proporciona garantías
- Un agente que no puede avanzar debe **escalar a un humano**, no buclar eternamente

---

## Trampa Común del Examen

> Un agente de soporte al cliente verificaba `response.content[0].type == "text"` para la finalización, perdiendo texto explicativo emparejado con bloques `tool_use`.

Respuesta: Reemplazar las verificaciones de contenido con verificación de `stop_reason`. Claude puede devolver texto y `tool_use` simultáneamente.

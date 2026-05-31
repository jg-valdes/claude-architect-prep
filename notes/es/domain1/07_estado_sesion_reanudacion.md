# Estado de Sesión y Reanudación

**Peso en el examen:** Dominio 1 – Arquitectura Agéntica y Orquestación (27%)

---

## Tres Opciones de Gestión de Sesión

### Opción 1: `--resume <nombre-sesion>`

Restaura una sesión nombrada con su historial completo de conversación, incluyendo todos los resultados de herramientas y análisis.

**Usar cuando:** El contexto previo es mayormente válido y los archivos permanecen sin cambios.

**Evitar cuando:** El código ha sido modificado — crea el **problema de contexto obsoleto**.

---

### Opción 2: `fork_session`

Crea **ramas independientes** desde una línea base de análisis compartida. Cada rama opera independientemente — los cambios en una no afectan a la otra.

**Usar cuando:** Se exploran enfoques divergentes desde un punto de partida compartido (ej., comparando dos estrategias de refactorización).

---

### Opción 3: Inicio Fresco con Inyección de Resumen

Lanza una sesión completamente nueva inyectando un resumen estructurado de los hallazgos previos.

**Usar cuando:**
- Los resultados de herramientas de la sesión previa son obsoletos (archivos cambiados, APIs actualizadas, dependencias modificadas)
- La sesión previa ha acumulado contexto excesivo

**Ventaja:** Elimina los resultados de herramientas obsoletos mientras preserva el conocimiento valioso.

---

## Matriz de Decisión

| Escenario | Mejor Enfoque | Razón |
|---|---|---|
| Continuar trabajo, sin cambios | `--resume` | Contexto válido, historial útil |
| Comparar enfoques | `fork_session` | Exploración divergente |
| Archivos modificados | Inicio fresco + resumen | Evita contexto obsoleto |
| Sesión larga y desordenada | Inicio fresco + resumen | Línea base limpia necesaria |

---

## El Problema de Contexto Obsoleto

Al reanudar después de modificaciones de archivos, el agente razona desde **resultados de herramientas en caché que muestran código antiguo** mientras accede a versiones nuevas de archivos simultáneamente — produciendo orientación contradictoria.

**Simplemente pedir al agente que re-lea los archivos modificados es insuficiente** porque los resultados de herramientas obsoletos permanecen en el historial de conversación.

**Solución confiable:** Sesión fresca con re-análisis dirigido limitado solo a los archivos cambiados.

---

## Puntos Clave del Examen

- `--resume` preserva el historial completo — **evitar después de modificaciones de código**
- `fork_session` = ramas divergentes desde una línea base compartida
- Inicio fresco + resumen = mejor para resultados de herramientas obsoletos
- Re-leer archivos dentro de una sesión reanudada **no** soluciona el historial obsoleto
- `fork_session` hereda datos de sesión obsoletos — NO usarlo para corregir obsolescencia

---

## Trampa Común del Examen

> "Después de modificar 3 de 50 archivos en una sesión, el agente da orientación contradictoria. ¿Cuál es el mejor enfoque?"

Respuesta: Iniciar fresco con un resumen inyectado especificando los tres archivos cambiados para re-análisis dirigido. Evita la re-exploración completa innecesaria mientras elimina el contexto obsoleto.

Respuestas incorrectas:
- Reanudar y pedir al agente re-leer los archivos modificados (los resultados de herramientas obsoletos permanecen en el historial)
- `fork_session` (hereda los datos obsoletos de la sesión)
- Re-exploración completa de la base de código (innecesaria cuando solo cambiaron 3 archivos)

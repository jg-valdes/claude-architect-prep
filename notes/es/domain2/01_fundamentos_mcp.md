# Fundamentos de MCP

**Peso en el examen:** Dominio 2 – Diseño de Herramientas e Integración MCP (18%)

---

## ¿Qué Es MCP?

El Model Context Protocol (MCP) extiende las capacidades de Claude conectándolo a sistemas externos — bases de datos, APIs, herramientas de desarrollo. Define una forma estándar de exponer herramientas, recursos y prompts a Claude.

---

## Respuestas de Error Estructuradas

Cuando las herramientas MCP fallan, las respuestas de error estructuradas permiten a los agentes recuperarse inteligentemente. El flag `isError` del protocolo MCP señala los fallos.

### Cuatro Categorías de Error

| Categoría | Descripción | `isRetryable` | Recuperación |
|---|---|---|---|
| **Transitorio** | Timeout, límite de tasa, servicio no disponible | `true` | Reintentar con retroceso exponencial |
| **Validación** | Solicitud malformada, formato inválido, campos faltantes | `true` | Corregir entrada y reintentar |
| **Negocio** | Violación de política, conflicto de reglas (solicitud válida, restricción incorrecta) | `false` | Escalar o usar flujo alternativo |
| **Permiso** | Acceso denegado, credenciales insuficientes | `false` | Escalar u obtener credenciales diferentes |

**Crítico:** Los errores de negocio tienen `isRetryable: false` — reintentar produce fallos idénticos.

---

## Fallo de Acceso vs. Resultado Vacío Válido

| Situación | `isError` | `resultCount` | Acción |
|---|---|---|---|
| La herramienta no puede alcanzar la fuente (timeout, fallo de auth) | `true` | — | Considerar reintento |
| Herramienta ejecutada exitosamente, no encontró coincidencias | `false` | `0` | Sin reintento — esta ES la respuesta |

Esta distinción **previene reintentos innecesarios**. Nunca reintentar un resultado vacío válido.

---

## Configuración del Servidor MCP

### Jerarquía de Alcance

| Archivo | Alcance | ¿Versionado? |
|---|---|---|
| `.mcp.json` (raíz del proyecto) | Todo el equipo | Sí — compartido via git |
| `~/.claude.json` | Personal | No — solo local |

**Principio clave:** Todas las herramientas de todos los servidores configurados (nivel proyecto y usuario) se descubren en tiempo de conexión y están disponibles simultáneamente.

### Expansión de Variables de Entorno

`.mcp.json` soporta la sintaxis `${NOMBRE_VARIABLE}` para mantener credenciales fuera del control de versiones:

```json
{
  "mcpServers": {
    "jira": {
      "command": "npx",
      "args": ["@empresa/mcp-jira", "--token", "${JIRA_TOKEN}"]
    }
  }
}
```

Cada desarrollador configura sus propios tokens localmente.

---

## Recursos MCP

Los recursos exponen catálogos de contenido a los agentes **sin requerir llamadas exploratorias a herramientas**:
- Resúmenes de issues con títulos y estados
- Jerarquías de documentación
- Esquemas de base de datos con nombres de tablas y relaciones

Los recursos reducen las consultas innecesarias al hacer la información inmediatamente disponible.

---

## Decisión Construir vs. Usar

**Usar servidores de la comunidad para:**
- Integraciones estándar (Jira, GitHub, Slack, Notion)
- Soluciones probadas que requieren mantenimiento mínimo

**Construir servidores personalizados solo cuando:**
- El equipo tiene flujos de trabajo específicos que los servidores comunitarios no pueden manejar
- Se necesita lógica de negocio personalizada en la capa de herramientas
- Integración con sistemas internos propietarios

---

## Mejora de Descripciones de Herramientas MCP

Las descripciones escasas hacen que los agentes prefieran herramientas integradas con documentación más rica. Las descripciones mejoradas deben tener **3–5 oraciones** explicando capacidades, salidas, casos de uso y comparaciones con alternativas.

---

## Puntos Clave del Examen

- Configuración de todo el equipo → `.mcp.json` (raíz del proyecto, versionado)
- Configuración personal/experimental → `~/.claude.json` (nunca se hace commit)
- Los errores de negocio **no son reintentables** — se evalúa frecuentemente
- Resultado vacío válido ≠ fallo de acceso — conocer la diferencia
- Siempre evaluar servidores MCP comunitarios antes de construir personalizados
- Hacer commit de `.mcp.json` con referencias a variables de entorno, nunca credenciales directas

---

## Trampa Común del Examen

> "¿La configuración de MCP de Jira va en `.mcp.json` o `~/.claude.json`?"

Respuesta: `.mcp.json` en la raíz del proyecto — es de todo el equipo y debe versionarse. `~/.claude.json` es solo para servidores personales/experimentales.

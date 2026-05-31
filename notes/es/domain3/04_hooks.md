# Hooks

**Peso en el examen:** Parte del Dominio 3 – Configuración y Flujos de Trabajo de Claude Code (20%)

---

## ¿Qué Son los Hooks?

Los hooks son comandos shell que Claude Code ejecuta automáticamente en respuesta a eventos específicos del ciclo de vida — antes o después del uso de herramientas, al detenerse, etc. Aplican comportamiento **de forma determinista**, a diferencia de las instrucciones CLAUDE.md que son probabilísticas.

---

## Eventos de Hook

| Evento | Se activa cuando... | ¿Puede bloquear? |
|---|---|---|
| `PreToolUse` | Antes de que Claude llame cualquier herramienta | Sí (salir con código no-cero) |
| `PostToolUse` | Después de que una llamada a herramienta se completa | No |
| `Notification` | Cuando Claude envía una notificación | No |
| `Stop` | Cuando Claude finaliza una respuesta | No |

---

## Configuración

Los hooks se definen en `settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command", "command": "echo 'A punto de ejecutar bash'" }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          { "type": "command", "command": "npm run lint -- --fix" }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          { "type": "command", "command": "notify-send 'Claude terminó'" }
        ]
      }
    ]
  }
}
```

---

## Ubicaciones de Archivos de Configuración

| Archivo | Alcance |
|---|---|
| `~/.claude/settings.json` | Global — todos los proyectos |
| `.claude/settings.json` | Proyecto — compartido con el equipo via git |
| `.claude/settings.local.json` | Anulación local — personal, no se hace commit |

**Prioridad:** local > proyecto > global

---

## El Campo `matcher`

```json
"matcher": "Bash"          // se activa en cualquier llamada a herramienta Bash
"matcher": "Write"         // se activa en llamadas a Write
"matcher": "Bash(npm *)"   // se activa en comandos bash que coinciden con el glob
```

Omitir `matcher` para activar en **todas** las llamadas a herramientas de ese tipo de evento.

---

## Hooks vs. CLAUDE.md

| Hooks | CLAUDE.md |
|---|---|
| Configurados en `settings.json` | Instrucciones de configuración para Claude |
| Ejecutados por el **sistema** (determinista) | Seguidos por Claude (probabilístico) |
| Disparadores de shell automatizados | Guía de comportamiento |
| No pueden ser ignorados por el modelo | Pueden omitirse ocasionalmente |

---

## Puntos Clave del Examen

- `PreToolUse` puede **bloquear** una llamada a herramienta (salir con código no-cero)
- `PostToolUse` no puede prevenir la acción — solo reaccionar a ella
- Los hooks viven en `settings.json`, **no** en CLAUDE.md
- Los hooks de proyecto (`.claude/settings.json`) se comparten con el equipo
- Los hooks locales (`.claude/settings.local.json`) son personales

---

## Trampa Común del Examen

> "Quieres ejecutar `prettier` automáticamente cada vez que Claude edita un archivo. ¿Dónde configuras esto?"

Respuesta: Hook `PostToolUse` con `"matcher": "Edit"` en `.claude/settings.json` — **no** en CLAUDE.md (CLAUDE.md son instrucciones para que Claude siga, no disparadores de shell automatizados).

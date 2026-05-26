# Hooks

**Peso en el examen:** Parte del Dominio 3 – Configuración y Flujos de Trabajo de Claude Code (20%)

---

## ¿Qué Son los Hooks?

Los hooks son comandos de shell que Claude Code ejecuta automáticamente en respuesta a eventos específicos — antes o después del uso de herramientas, al detenerse, etc. Permiten automatizar reacciones a las acciones de Claude sin escribirlas en CLAUDE.md.

---

## Eventos de Hook

| Evento | Se activa cuando... |
|---|---|
| `PreToolUse` | Antes de que Claude llame a cualquier herramienta |
| `PostToolUse` | Después de que se completa una llamada a herramienta |
| `Notification` | Cuando Claude envía una notificación |
| `Stop` | Cuando Claude termina una respuesta |

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
          {
            "type": "command",
            "command": "echo 'A punto de ejecutar un comando bash'"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "npm run lint -- --fix"
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "notify-send 'Claude terminó'"
          }
        ]
      }
    ]
  }
}
```

---

## Ubicaciones del Archivo de Configuración

| Archivo | Alcance |
|---|---|
| `~/.claude/settings.json` | Global — aplica a todos los proyectos |
| `.claude/settings.json` | Proyecto — subido al repositorio, compartido con el equipo |
| `.claude/settings.local.json` | Anulación local — no se sube, personal |

**Prioridad:** local > proyecto > global (mismo patrón que CLAUDE.md)

---

## El Campo `matcher`

El `matcher` filtra qué herramienta activa el hook:

```json
"matcher": "Bash"        // solo se activa en llamadas a la herramienta Bash
"matcher": "Write"       // solo se activa en llamadas a la herramienta Write
"matcher": "Bash(npm *)" // solo se activa en comandos bash que coincidan con el glob
```

Omite `matcher` completamente para activar en **todas** las llamadas de ese tipo de evento.

---

## Casos de Uso Prácticos

- **Auto-lint al escribir archivos:** Ejecutar `eslint --fix` cada vez que Claude escriba un archivo JS/TS
- **Auto-formato:** Ejecutar `prettier` después de cada `Edit` o `Write`
- **Logging:** Registrar todos los comandos Bash que ejecuta Claude para auditoría
- **Notificaciones:** Enviar una notificación de escritorio cuando Claude termine una tarea larga
- **Bloquear comandos peligrosos:** Usar `PreToolUse` para interceptar y rechazar operaciones de riesgo

---

## Puntos Clave para el Examen

- Los hooks se configuran en **`settings.json`**, no en `CLAUDE.md`
- `PreToolUse` puede **bloquear** una llamada a herramienta saliendo con código distinto de cero
- `PostToolUse` se ejecuta **después** de la herramienta, por lo que no puede prevenir la acción — solo reaccionar
- El campo `matcher` usa la misma sintaxis de **patrón glob** que los permisos
- Los hooks de proyecto (`.claude/settings.json`) se **comparten con el equipo**; los hooks locales (`.claude/settings.local.json`) son personales

---

## Trampa Común en el Examen

> "Quieres ejecutar `prettier` automáticamente cada vez que Claude edita un archivo. ¿Dónde configuras esto?"

Respuesta: Como un hook `PostToolUse` con `"matcher": "Edit"` en `.claude/settings.json` — **no** en CLAUDE.md (CLAUDE.md son instrucciones para que Claude las siga, no disparadores de shell automatizados).

# Modelo de Permisos

**Peso en el examen:** Parte del Dominio 3 – Configuración y Flujos de Trabajo de Claude Code (20%)

---

## ¿Qué Es el Modelo de Permisos?

Claude Code solicita aprobación del usuario antes de ejecutar herramientas potencialmente riesgosas. El modelo de permisos te permite pre-aprobar (allow) o bloquear (deny) herramientas y comandos específicos para que Claude no pause a preguntar cada vez.

---

## Configuración

Los permisos viven en `settings.json`:

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Edit",
      "Write",
      "Bash(npm run *)",
      "Bash(git *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)"
    ]
  }
}
```

---

## Sintaxis de Patrones Glob

Para comandos `Bash`, puedes hacer coincidir comandos específicos con globs:

```
"Bash(npm *)"          → cualquier comando npm
"Bash(npm run *)"      → solo comandos npm run
"Bash(git diff)"       → solo exactamente `git diff`
"Bash(git *)"          → cualquier comando git
"Read"                 → permitir herramienta Read (sin glob para herramientas no-Bash)
```

---

## Orden de Resolución de Permisos

Cuando Claude quiere usar una herramienta:

1. Verificar lista **deny** — si coincide, **bloquear** (deny siempre gana)
2. Verificar lista **allow** — si coincide, **permitir sin preguntar**
3. Ninguna coincide → **preguntar al usuario**

**Deny tiene prioridad sobre allow** — si un comando coincide con ambos, se deniega.

---

## Alcance de los Archivos de Configuración

| Archivo | Alcance | ¿Bajo control de versiones? |
|---|---|---|
| `~/.claude/settings.json` | Global (todos los proyectos) | No |
| `.claude/settings.json` | Proyecto (compartido) | Sí |
| `.claude/settings.local.json` | Anulación local (personal) | No |

**Prioridad:** local > proyecto > global

---

## Referencia de Nombres de Herramientas

| Herramienta | Qué hace |
|---|---|
| `Read` | Leer archivos |
| `Edit` | Editar archivos (basado en diff) |
| `Write` | Escribir/crear archivos |
| `Bash` | Ejecutar comandos de shell |
| `WebSearch` | Buscar en la web |
| `WebFetch` | Obtener una URL |
| `Agent` | Crear subagentes |

---

## Puntos Clave para el Examen

- **Deny siempre gana** — si un comando está en allow y deny, se deniega
- Los permisos son **aditivos entre niveles** — proyecto se suma al global, local se suma al proyecto
- `Bash(npm *)` y `Bash(npm run *)` son **diferentes** — el segundo es más restrictivo
- Sin reglas allow, Claude solicita permiso en cada uso de herramienta
- En CI/CD, usa el flag `--allowedTools` en lugar de settings.json para control por comando

---

## Trampa Común en el Examen

> "Un settings.json de proyecto tiene `"allow": ["Bash(git *)"]` y el settings.json global tiene `"deny": ["Bash(git push *)"]`. ¿Qué sucede cuando Claude intenta ejecutar `git push`?"

Respuesta: **Denegado.** Las reglas deny tienen prioridad sobre las allow, y todos los niveles se combinan — el deny global bloquea el allow del proyecto.

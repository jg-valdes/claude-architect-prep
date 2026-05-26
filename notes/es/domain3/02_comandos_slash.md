# Comandos Slash Personalizados

**Peso en el examen:** Parte del Dominio 3 – Configuración y Flujos de Trabajo de Claude Code (20%)

---

## ¿Qué Son los Comandos Slash Personalizados?

Los comandos slash personalizados te permiten guardar prompts reutilizables como archivos que se pueden invocar con `/nombre-comando` en Claude Code. Son útiles para tareas repetitivas como despliegues, generación de descripciones de PRs o revisiones de código.

---

## Estructura de Archivos

Los comandos se almacenan como archivos Markdown:

```
.claude/commands/           ← Comandos a nivel de proyecto (compartidos con el equipo)
  deploy.md                 → /deploy
  run-tests.md              → /run-tests
  review-pr.md              → /review-pr

~/.claude/commands/         ← Comandos globales (personales, todos los proyectos)
  mi-plantilla.md           → /mi-plantilla
```

**Regla de nombres:** El nombre del archivo (sin `.md`) se convierte en el nombre del comando slash.

---

## Formato del Archivo de Comando

Un archivo de comando es simplemente un archivo Markdown con el prompt que Claude ejecutará:

```markdown
# Desplegar en Entorno

Despliega la rama actual en $ARGUMENTS. Antes de desplegar:
1. Ejecuta `npm run build` y confirma que pasa
2. Ejecuta `npm test` y confirma que todas las pruebas pasan
3. Muéstrame el git diff contra main
4. Pide confirmación antes de continuar
```

Se invoca como: `/deploy staging` o `/deploy production`

---

## Usando `$ARGUMENTS`

`$ARGUMENTS` es un marcador especial que captura todo lo escrito después del nombre del comando.

```bash
/deploy staging       → $ARGUMENTS = "staging"
/review-pr 42         → $ARGUMENTS = "42"
/generate-test utils  → $ARGUMENTS = "utils"
```

Si no se pasan argumentos, `$ARGUMENTS` es una cadena vacía.

---

## Alcance: Proyecto vs Global

| Ubicación | Alcance | Caso de uso |
|---|---|---|
| `.claude/commands/` | Solo proyecto | Flujos de trabajo del equipo, tareas específicas del repositorio |
| `~/.claude/commands/` | Todos los proyectos | Comandos de productividad personal |

Los comandos de proyecto tienen precedencia sobre los globales si los nombres coinciden.

---

## Puntos Clave para el Examen

- Los comandos son simplemente **archivos Markdown** — sin sintaxis especial más allá de `$ARGUMENTS`
- Se ejecutan como **prompts** — Claude lee el contenido del archivo y actúa en consecuencia
- `.claude/commands/` debe **subirse al control de versiones** para compartir con el equipo
- `~/.claude/commands/` es **personal** y no se comparte
- El nombre del comando viene del **nombre del archivo**, no del contenido dentro del archivo

---

## Trampa Común en el Examen

> "¿Dónde debes poner un comando slash al que todos los miembros del equipo deban tener acceso?"

Respuesta: `.claude/commands/` (nivel de proyecto, subido al repositorio) — **no** `~/.claude/commands/` que es personal/global.

# Jerarquía de CLAUDE.md

**Peso en el examen:** Parte del Dominio 3 – Configuración y Flujos de Trabajo de Claude Code (20%)

---

## La Jerarquía de Tres Niveles

| Nivel | Ubicación | ¿Versionado? | Alcance |
|---|---|---|---|
| Usuario | `~/.claude/CLAUDE.md` | No | Solo ese desarrollador |
| Proyecto | `.claude/CLAUDE.md` o `CLAUDE.md` raíz | Sí | Todos los miembros del equipo |
| Directorio | `packages/api/CLAUDE.md` (cualquier subdirectorio) | Sí | Solo ese directorio |

---

## Orden de Carga y Manejo de Conflictos

Los archivos se **concatenan en el contexto**, no se anulan entre sí. La carga va del alcance más amplio al más específico — el contenido más cercano al directorio de trabajo se lee al final.

Dentro de un directorio, `CLAUDE.local.md` se agrega **después** de `CLAUDE.md`.

**Crítico:** "Si dos reglas se contradicen, Claude puede elegir una arbitrariamente." CLAUDE.md proporciona guía sin aplicación estricta.

Para **cumplimiento garantizado** → usar `settings.json` (aplicado por el cliente) o hooks (vinculados al ciclo de vida).

---

## Organización Modular con Importaciones de Ruta @

Los archivos CLAUDE.md grandes pueden dividirse usando la sintaxis `@` (sin palabra clave `@import`):

```markdown
# .claude/CLAUDE.md
Estándares de codificación:
@./standards/convenciones-nomenclatura.md
@./standards/manejo-errores.md
@./standards/requisitos-pruebas.md
```

Los archivos se cargan **inmediatamente** — los archivos referenciados se insertan en tiempo de carga. Esto mantiene el mantenimiento del código fuente limpio pero **no** reduce el tamaño del contexto de sesión.

---

## CLAUDE.local.md

- Se carga después de `CLAUDE.md` en el mismo nivel
- Convencionalmente ignorado en git para ajustes personales
- Para reglas del equipo, usar `CLAUDE.md` en su lugar

---

## El Directorio `.claude/rules/`

Alternativa a un único CLAUDE.md — contiene archivos específicos por tema como `testing.md`, `api-conventions.md`, `deployment.md`. Los archivos pueden incluir frontmatter YAML para alcance por rutas.

---

## El Comando `/memory`

`/memory` **revela qué archivos de configuración ya están cargados**. NO carga archivos de configuración.

La configuración se carga automáticamente al inicio de la sesión — `/memory` es una herramienta de diagnóstico, no un mecanismo de activación.

---

## Puntos Clave del Examen

- Nuevo miembro del equipo no recibe instrucciones → la configuración está en nivel **usuario**, no de proyecto
- La configuración de nivel usuario no se sincroniza via git — permanece en una máquina
- Las importaciones `@` insertan contenido inmediatamente — no ahorra presupuesto de contexto
- Reglas contradictorias → Claude elige arbitrariamente; usar `settings.json` para aplicación garantizada
- `/memory` = diagnosticar qué está cargado, no activarlo

---

## Trampa Común del Examen

> "Los nuevos desarrolladores clonan el repositorio pero obtienen comportamiento diferente en Claude Code que el ingeniero senior."

Respuesta: Las convenciones del ingeniero senior están en `~/.claude/CLAUDE.md` (nivel usuario), no en `.claude/CLAUDE.md` (nivel proyecto). Solución: mover al nivel de proyecto para que se sincronice via git.

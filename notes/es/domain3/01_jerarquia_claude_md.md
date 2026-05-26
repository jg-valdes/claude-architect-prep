# Jerarquía de CLAUDE.md

**Peso en el examen:** Parte del Dominio 3 – Configuración y Flujos de Trabajo de Claude Code (20%)

---

## ¿Qué es CLAUDE.md?

`CLAUDE.md` es el archivo de configuración principal de Claude Code. Proporciona contexto persistente e instrucciones que Claude lee al inicio de cada sesión. Piénsalo como un "README del proyecto para Claude".

---

## Ubicaciones de Archivos (Jerarquía)

Claude lee y fusiona archivos CLAUDE.md de múltiples niveles:

```
~/.claude/CLAUDE.md                  ← 1. Global (todos los proyectos)
{raíz_proyecto}/CLAUDE.md            ← 2. Nivel de proyecto
{raíz_proyecto}/.claude/CLAUDE.md    ← 2. También nivel de proyecto
{raíz_proyecto}/{subdir}/CLAUDE.md   ← 3. Subdirectorio (anulación local)
```

**Regla de prioridad:** Más específico = mayor prioridad.
Un `CLAUDE.md` de subdirectorio anula al del proyecto. Uno de proyecto anula al global.

Todos los archivos se **fusionan**, no se reemplazan — las instrucciones globales siguen aplicando a menos que sean explícitamente contradichas.

---

## Qué Incluir en CLAUDE.md

### Comandos
```markdown
## Construcción y Pruebas
- Construir: `npm run build`
- Pruebas: `npm test`
- Lint: `npm run lint`
```

### Convenciones
```markdown
## Estilo de Código
- Usar TypeScript en modo estricto
- Preferir `const` sobre `let`
- Sin tipos `any`
```

### Notas de arquitectura
```markdown
## Arquitectura
- La autenticación se maneja en `src/middleware/auth.ts`
- Nunca escribir directamente a la BD — siempre usar la capa de repositorio
```

### Instrucciones de comportamiento
```markdown
## Instrucciones para Claude
- Siempre ejecutar las pruebas antes de marcar una tarea como completada
- Nunca modificar archivos de migración
- Preguntar antes de eliminar cualquier archivo
```

---

## Puntos Clave para el Examen

- `CLAUDE.md` se **carga automáticamente** — no necesitas referenciarlo en los prompts
- Múltiples archivos se **fusionan**, con archivos más específicos tomando precedencia
- Está pensado para **subirse al control de versiones** para que todo el equipo se beneficie
- `~/.claude/CLAUDE.md` global es para preferencias personales que aplican en todas partes
- Los archivos `CLAUDE.md` de subdirectorio solo se activan cuando Claude trabaja **dentro de ese directorio**

---

## Trampa Común en el Examen

> "¿Qué CLAUDE.md tiene precedencia cuando hay un conflicto?"

Respuesta: **El más específico** (subdirectorio > proyecto > global). Pero recuerda — las instrucciones no conflictivas de todos los niveles siguen fusionándose y aplicando.

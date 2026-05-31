# Comandos Slash Personalizados y Skills

**Peso en el examen:** Parte del Dominio 3 – Configuración y Flujos de Trabajo de Claude Code (20%)

---

## Crear Comandos Personalizados

Los comandos creados en `.claude/skills/` o `.claude/commands/` producen `/comandos` idénticos que los desarrolladores pueden invocar. Ambos nombres de directorio funcionan.

### Niveles de Alcance

| Ubicación | Alcance | ¿Versionado? |
|---|---|---|
| `.claude/skills/` o `.claude/commands/` | Proyecto — todos los miembros del equipo | Sí |
| `~/.claude/skills/` o `~/.claude/commands/` | Personal — no compartido | No |

---

## Tres Opciones Críticas de Frontmatter

```yaml
---
context: fork
allowed-tools: Read, Grep
argument-hint: "archivo a analizar"
---
```

| Opción | Efecto |
|---|---|
| `context: fork` | Ejecuta el skill en contexto de sub-agente aislado — la salida verbosa no contamina la conversación principal |
| `allowed-tools` | Restringe qué herramientas puede usar el skill (límite de seguridad) |
| `argument-hint` | Solicita al desarrollador parámetros requeridos cuando se invoca sin argumentos |

---

## Skills vs. CLAUDE.md

| Skills | CLAUDE.md |
|---|---|
| Flujos de trabajo específicos de tarea, bajo demanda | Estándares universales, siempre cargados |
| Cargados solo cuando se invocan | Aplicados en cada sesión |
| Usar para: "ejecutar una revisión", "generar un informe" | Usar para: convenciones de nomenclatura, estándares de manejo de errores |

**Anti-patrón:** Colocar procedimientos específicos de tarea en CLAUDE.md.

---

## Reglas Específicas por Ruta (`.claude/rules/`)

Los archivos de reglas usan frontmatter YAML con un campo `paths` que especifica qué archivos activan la carga:

```yaml
---
paths: ["terraform/**/*"]
---
# Convenciones de Terraform
- Usar snake_case para nombres de recursos
- Etiquetar cada recurso con etiquetas de entorno y equipo
```

**Ventaja clave:** Los patrones glob como `**/*.test.tsx` capturan archivos de prueba en toda la base de código, independientemente de la profundidad del directorio.

### Cuándo Usar Cada Enfoque

| Escenario | Mejor Enfoque |
|---|---|
| Estándares universales del equipo | CLAUDE.md raíz |
| Directorio de un solo paquete | CLAUDE.md a nivel de directorio |
| Tipo de archivo en muchos directorios | Reglas específicas por ruta con patrones glob |
| Flujos de trabajo de tarea bajo demanda | Skills en `.claude/skills/` |

---

## Eficiencia de Tokens

Las reglas con alcance por ruta se cargan **solo al editar archivos coincidentes**. CLAUDE.md raíz se carga en cada sesión. Para proyectos grandes con múltiples categorías de convenciones, las reglas con alcance por ruta preservan el presupuesto de tokens.

---

## Puntos Clave del Examen

- Comandos de alcance de proyecto → `.claude/skills/` (compartidos via git)
- Comandos personales → `~/.claude/skills/` (no versionados)
- `context: fork` previene que la salida verbosa del skill contamine la conversación principal
- Los skills son **bajo demanda**; CLAUDE.md es **siempre activo**
- Reglas específicas por ruta en `.claude/rules/` con frontmatter `paths` = convenciones de tipo de archivo

---

## Trampas Comunes del Examen

- Confundir rutas de alcance usuario (`~/.claude/`) con alcance de proyecto (`.claude/`) para comandos del equipo
- Tratar los skills como guía siempre activa como CLAUDE.md
- Olvidar `context: fork` para salidas verbosas
- Colocar flujos de trabajo específicos de tarea en CLAUDE.md en lugar de skills
- Elegir CLAUDE.md a nivel de directorio sobre reglas específicas por ruta cuando las convenciones aplican en 50+ ubicaciones

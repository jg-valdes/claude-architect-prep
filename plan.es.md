# Plan de Estudio CCA-F

**Objetivo:** Aprobar el examen Claude Certified Architect – Foundations (CCA-F)
**Formato:** 60 preguntas · 120 min · Puntuación para aprobar: 720/1000 · Costo: $99

---

## Dominios del Examen

| Dominio | Peso | Prioridad |
|---|---|---|
| Arquitectura Agéntica y Orquestación | 27% | Alta |
| Configuración y Flujos de Trabajo de Claude Code | 20% | Alta |
| Ingeniería de Prompts y Salida Estructurada | 20% | Alta |
| Diseño de Herramientas e Integración MCP | 18% | Media |
| Gestión de Contexto y Fiabilidad | 15% | Media |

---

## Cómo Usar Este Repositorio

Cada día cubre uno o más dominios. Para cada dominio:

1. Lee las notas en `notes/es/dominioX/` a tu propio ritmo
2. Vuelve a Claude Code y di **"ponme a prueba"** — Claude ejecutará un quiz estilo examen sobre lo que acabas de leer
3. Marca los elementos completados
4. Avanza al siguiente dominio

Las notas y los quizzes juntos cubren los 5 dominios del examen con el mismo estilo de pregunta que el examen real (4 opciones, varias parecen válidas — elige la mejor).

---

## Día 1: Fundamentos — Dominio 3

- [ ] Lee la guía oficial del examen: https://www.claudedirectory.org/blog/anthropic-claude-certification-program
- [ ] Lee la guía de estudio CCA-F: https://tutorialsdojo.com/cca-f-claude-certified-architect-foundations-study-guide/
- [ ] Comienza Claude Code in Action en Anthropic Academy: https://anthropic.skilljar.com/claude-code-in-action
- [ ] Estudia el Dominio 3 – Configuración y Flujos de Trabajo de Claude Code
  - [ ] `notes/es/domain3/01_jerarquia_claude_md.md`
  - [ ] `notes/es/domain3/02_comandos_slash.md`
  - [ ] `notes/es/domain3/03_integracion_cicd.md`
  - [ ] `notes/es/domain3/04_hooks.md`
  - [ ] `notes/es/domain3/05_modelo_permisos.md`
- [ ] Sesión de quiz con Claude sobre los temas del Dominio 3

## Día 2: Arquitectura — Dominios 1 y 5

- [ ] Estudia el Dominio 1 – Arquitectura Agéntica y Orquestación (27% — el dominio más grande)
  - [ ] `notes/es/domain1/01_patrones_multiagente.md`
  - [ ] `notes/es/domain1/02_descomposicion_tareas.md`
  - [ ] `notes/es/domain1/03_bucles_agentes.md`
  - [ ] `notes/es/domain1/04_coordinacion_subagentes.md`
  - [ ] `notes/es/domain1/05_patrones_fiabilidad.md`
- [ ] Estudia el Dominio 5 – Gestión de Contexto y Fiabilidad
  - [ ] `notes/es/domain5/01_gestion_ventana_contexto.md`
  - [ ] `notes/es/domain5/02_fiabilidad_tareas_largas.md`
- [ ] Sesión de quiz con Claude sobre los temas de los Dominios 1 y 5

## Día 3: MCP y Prompts — Dominios 2 y 4

- [ ] Estudia el Dominio 2 – Diseño de Herramientas e Integración MCP
  - [ ] `notes/es/domain2/01_fundamentos_mcp.md`
  - [ ] `notes/es/domain2/02_stdio_vs_sse.md`
  - [ ] `notes/es/domain2/03_diseno_esquema_herramientas.md`
- [ ] Estudia el Dominio 4 – Ingeniería de Prompts y Salida Estructurada
  - [ ] `notes/es/domain4/01_ingenieria_prompts.md`
  - [ ] `notes/es/domain4/02_patrones_salida_estructurada.md`
- [ ] Sesión de quiz con Claude sobre los temas de los Dominios 2 y 4

## Día 4: Exámenes de Práctica

- [ ] Toma el examen de práctica #1: https://claudecertifications.com/
- [ ] Revisa todas las respuestas incorrectas — identifica dominios débiles
- [ ] Toma el examen de práctica #2: https://certificationpractice.com/practice-exams/anthropic-claude-certified-architect-foundations
- [ ] Revisa todas las respuestas incorrectas
- [ ] Toma el examen de práctica #3: https://certstud.com/certifications/anthropic/claude-architect
- [ ] Anota los dominios débiles recurrentes para la revisión del Día 5

## Día 5: Revisión Dirigida + Reserva del Examen

- [ ] Revisa todos los dominios débiles identificados el Día 4
- [ ] Relee los escenarios clave de decisión (el examen elige opciones donde 3 de 4 parecen válidas)
- [ ] Sesión final de quiz con Claude: Q&A simulacro completo de todos los dominios
- [ ] Reserva el examen en https://anthropic.skilljar.com/
- [ ] Revisa la logística del examen (supervisado, 120 min, sin notas)

## Día 6: Día del Examen

- [ ] Revisión ligera de notas — sin estudiar en exceso
- [ ] Toma el examen CCA-F
- [ ] Obtén la certificación

---

## Hoja de Referencia Rápida

### Claude Code (Dominio 3)
- `CLAUDE.md` es el archivo de configuración principal — soporta global (`~/.claude/CLAUDE.md`), proyecto (`.claude/CLAUDE.md`) y anulaciones locales
- El `CLAUDE.md` más específico gana en conflictos; todos los niveles se fusionan
- Comandos slash: `.claude/commands/<nombre>.md` → `/nombre`, usa `$ARGUMENTS` para entrada
- CI/CD: flag `--print` / `-p` requerido para modo no interactivo
- Hooks en `settings.json`: `PreToolUse` puede bloquear, `PostToolUse` reacciona
- Permisos: deny siempre gana a allow; `--allowedTools` es una lista blanca

### Arquitectura Agéntica (Dominio 1)
- **Hub-and-spoke**: un orquestador delega a subagentes especializados
- **Los subagentes son sin estado** — el orquestador debe pasar todo el contexto explícitamente
- Define criterios de éxito y conteo máximo de iteraciones antes de iniciar cualquier bucle
- Mínima huella: solicita solo los permisos necesarios, prefiere acciones reversibles
- La escalada humana es correcta para acciones irreversibles o ambiguas

### Gestión de Contexto (Dominio 5)
- Resume el historial de conversación cuando el contexto se llene — no reinicies
- Guarda checkpoints de tareas largas después de cada paso principal
- Fragmentación: divide entradas grandes, procesa cada una independientemente, agrega resultados
- Las operaciones idempotentes hacen que los reintentos sean seguros

### MCP (Dominio 2)
- Protocolo para conectar Claude a herramientas externas y fuentes de datos
- **stdio**: subproceso local, sin autenticación, un cliente — simple
- **SSE**: HTTP remoto, requiere autenticación, muchos clientes — compartible/escalable
- Las descripciones de herramientas guían el comportamiento de selección de Claude
- Usa `enum` en esquemas para restringir valores válidos

### Ingeniería de Prompts (Dominio 4)
- Usa etiquetas XML para estructurar prompts complejos
- Los ejemplos few-shot van **antes** de la entrada real
- Tres capas: solo prompt (más débil) → esquema en prompt → tool_use API (más fuerte)
- Siempre valida la salida estructurada y reintenta con el mensaje de error específico
- Repite instrucciones críticas de formato al final del prompt (sesgo de recencia)

---

## Guía de Decisión Rápida

| Problema | Respuesta |
|---|---|
| Selección de herramienta incorrecta | Arregla la **descripción** |
| Valores de salida de herramienta inválidos | Añade **enum** al esquema |
| Salida JSON poco fiable | Usa aplicación **tool_use** de API |
| MCP multi-cliente | Transporte **SSE** |
| MCP local/un usuario | Transporte **stdio** |
| Tarea larga falla a mitad | **Checkpointing** |
| Acción irreversible | **Escalada humana** |
| Subagentes compartiendo estado | No pueden — **el orquestador pasa todo** |
| Ventana de contexto llena | **Resumir** historial |
| Entrada grande no cabe | **Fragmentación** |

---

## Recursos

| Recurso | Tipo | Costo |
|---|---|---|
| [Anthropic Academy – Claude Code en Acción](https://anthropic.skilljar.com/claude-code-in-action) | Curso oficial | Gratis |
| [Guía de Estudio CCA-F – Tutorials Dojo](https://tutorialsdojo.com/cca-f-claude-certified-architect-foundations-study-guide/) | Guía de estudio | Gratis |
| [claudecertifications.com](https://claudecertifications.com/) | Preguntas de práctica | Gratis |
| [certificationpractice.com](https://certificationpractice.com/practice-exams/anthropic-claude-certified-architect-foundations) | 360 preguntas de práctica | Gratis/Pago |
| [certstud.com](https://certstud.com/certifications/anthropic/claude-architect) | 300+ preguntas de práctica | Gratis |

# Integración CI/CD con Claude Code

**Peso en el examen:** Parte del Dominio 3 – Configuración y Flujos de Trabajo de Claude Code (20%)

---

## El Flag `-p` / `--print`

**El hecho más directamente evaluable del Dominio 3.**

Sin `-p`, Claude Code se queda esperando entrada de teclado en pipelines CI. El flag habilita la ejecución no interactiva — procesa el prompt y sale automáticamente.

```bash
claude -p "Revisa estos cambios por problemas de seguridad" --output-format json
```

---

## Flags de Salida Estructurada

Dos flags trabajan juntos para salida legible por máquinas:

| Flag | Propósito |
|---|---|
| `--output-format json` | Emitir JSON en lugar de prosa |
| `--json-schema <archivo>` | Validar salida contra un esquema |

Estos habilitan a los sistemas automatizados a:
- Publicar comentarios en línea en PRs
- Filtrar hallazgos por severidad
- Rastrear problemas entre ejecuciones

---

## Aislamiento del Contexto de Sesión

> "La misma sesión de Claude que generó código es menos efectiva revisando sus propios cambios."

La sesión retiene el contexto de razonamiento de la generación de código — llevando a sesgo de confirmación en la revisión. Las instancias de revisión independientes evalúan código **sin sesgo de justificación previa**.

**Enfoque correcto:** Lanzar instancias separadas de Claude Code para generación y revisión.

---

## Contexto de Revisión Incremental

Las revisiones automatizadas deben rastrear hallazgos previos y reportar **solo problemas nuevos o sin resolver**.

Sin esto: los comentarios duplicados aparecen en cada push, erosionando la confianza del desarrollador.

---

## CLAUDE.md para CI

Los archivos de contexto del proyecto proporcionan a Claude Code invocado por CI:
- Estándares de prueba y fixtures disponibles
- Criterios de revisión y áreas de enfoque
- Información de cobertura de pruebas existente

Esto previene la generación de código repetitivo de bajo valor en contextos automatizados.

---

## API Síncrona vs. API de Lotes

| Caso de Uso | API | Latencia |
|---|---|---|
| Verificaciones pre-merge (desarrolladores esperan) | Síncrona | Tiempo real |
| Informes nocturnos, auditorías semanales | API de Lotes | Hasta 24 horas |
| Flujos de trabajo agénticos con llamadas a herramientas | Síncrona | N/A (Lotes no puede hacer esto) |

**Ahorro de la API de Lotes:** 50% de reducción de costo — pero usar solo para flujos de trabajo tolerantes a latencia.

**Crítico:** La API de Lotes no puede soportar llamadas a herramientas multi-turno dentro de una sola solicitud.

---

## Puntos Clave del Examen

- Flag `-p` = modo no interactivo — **requerido** para todo uso en CI
- `--output-format json` + `--json-schema` = salida legible por máquinas
- Auto-revisión (misma sesión) < revisión de instancia independiente
- API de Lotes = 50% más barata pero hasta 24h de latencia y **sin** llamadas a herramientas
- La API de Lotes es **incorrecta** para flujos de trabajo bloqueantes pre-merge

---

## Trampas Comunes del Examen

- Solucionar un trabajo CI colgado con variables de entorno o redirección stdin (es el flag `-p` faltante)
- Asumir que la misma sesión revisa su propia salida tan efectivamente como una instancia independiente
- Usar la API de Lotes para verificaciones pre-merge donde los desarrolladores están esperando
- No rastrear hallazgos previos, causando comentarios duplicados en cada push

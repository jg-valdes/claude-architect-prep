# Integración CI/CD con Claude Code

**Peso en el examen:** Parte del Dominio 3 – Configuración y Flujos de Trabajo de Claude Code (20%)

---

## Modo No Interactivo

Claude Code está diseñado para ejecutarse en pipelines automatizadas. La clave es usar el **modo no interactivo** para que no solicite entrada del usuario ni muestre una interfaz.

```bash
claude --print "revisa este PR en busca de problemas de seguridad"
claude -p "genera una prueba para la función en src/utils.ts"
```

`--print` (o `-p`) le indica a Claude que:
- Envíe el resultado a stdout
- Salga inmediatamente al terminar
- Nunca solicite entrada del usuario

---

## Flags Clave para CI/CD

| Flag | Propósito |
|---|---|
| `--print` / `-p` | Modo no interactivo, salida a stdout |
| `--model` | Especificar qué modelo usar (ej. `claude-opus-4-7`) |
| `--allowedTools` | Lista blanca de herramientas específicas que Claude puede usar |
| `--system-prompt` | Anular el prompt del sistema |
| `--no-interactive` | Mismo efecto que `--print` |

### Ejemplo: Restringir herramientas en CI
```bash
claude -p "revisa el diff en busca de errores" \
  --allowedTools "Read,Bash(git diff)" \
  --model claude-sonnet-4-6
```

---

## Casos de Uso Comunes en CI/CD

### Revisión automática de PRs
```yaml
# Ejemplo de GitHub Actions
- name: Revisión de Claude Code
  run: |
    claude -p "Revisa el git diff en busca de errores y problemas de seguridad.
    Genera un resumen en markdown." \
    --allowedTools "Read,Bash(git *)"
```

### Generación de pruebas al hacer commit
```bash
claude -p "Genera pruebas unitarias para las nuevas funciones del último commit" \
  --allowedTools "Read,Write,Bash(git show HEAD)"
```

### Documentación automatizada
```bash
claude -p "Actualiza CHANGELOG.md basándote en los commits desde el último tag" \
  --allowedTools "Read,Write,Bash(git log *)"
```

---

## Configuración para Entornos CI

En CI normalmente quieres pre-aprobar herramientas para que Claude no pause esperando permiso. Usa `settings.json`:

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Bash(git *)",
      "Bash(npm run *)"
    ]
  }
}
```

O pasa `--allowedTools` en línea para mantenerlo explícito por comando.

---

## Puntos Clave para el Examen

- `--print` / `-p` es **requerido** para uso no interactivo/CI — sin él Claude abrirá la interfaz interactiva
- `--allowedTools` es la forma correcta de **restringir el acceso a herramientas** en contextos automatizados
- Claude Code puede usarse en **cualquier sistema CI** (GitHub Actions, GitLab CI, Jenkins, etc.) — es solo una CLI
- Siempre restringe las herramientas al **mínimo necesario** para la tarea (principio de mínimo privilegio)

---

## Trampa Común en el Examen

> "Quieres ejecutar Claude Code en un pipeline de GitHub Actions sin interacción del usuario. ¿Qué flag es necesario?"

Respuesta: `--print` (o `-p`). Sin él, Claude Code entra en modo interactivo y el pipeline se bloqueará indefinidamente.

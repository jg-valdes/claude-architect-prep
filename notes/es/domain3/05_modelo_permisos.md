# Modelo de Permisos y Modo Plan

**Peso en el examen:** Parte del Dominio 3 – Configuración y Flujos de Trabajo de Claude Code (20%)

---

## Modelo de Permisos

Los permisos controlan qué herramientas puede usar Claude Code. Se configuran en `settings.json`.

### Allow vs. Deny

```json
{
  "permissions": {
    "allow": ["Bash(git *)", "Read", "Write"],
    "deny": ["Bash(rm *)", "Bash(sudo *)"]
  }
}
```

**Precedencia:** Las reglas `deny` tienen prioridad sobre las reglas `allow` cuando se superponen.

### Flag `--allowedTools`

Se pasa en tiempo de invocación para limitar herramientas para esa sesión:

```bash
claude --allowedTools "Read,Grep,Bash(git log *)" -p "Resume los cambios recientes"
```

Actúa como una **lista blanca** — solo las herramientas listadas están disponibles. Todas las demás están implícitamente denegadas.

---

## Modo Plan vs. Ejecución Directa

### La Distinción Central

**La decisión no es sobre dificultad — es sobre ambigüedad.**

Una corrección de bug difícil pero bien definida (traza de stack clara, función única, causa conocida) → **ejecución directa**.

Una reestructuración multi-archivo aparentemente simple pero ambigua → **modo plan**.

---

### Cuándo Usar Modo Plan

- Cambios arquitecturales a gran escala (reestructuración de monolito, reorganización de módulos)
- Tareas con múltiples enfoques de implementación válidos
- Modificaciones multi-archivo (45+ archivos) que necesitan estrategia consistente
- Exploración de la base de código necesaria antes de los cambios
- Situaciones donde las decisiones arquitecturales tienen consecuencias posteriores

> El modo plan permite exploración y diseño seguros. Claude lee la base de código, analiza dependencias y propone un enfoque — **todo sin modificar ningún archivo**.

---

### Cuándo Usar Ejecución Directa

- Cambios bien delimitados con trazas de stack claras
- El enfoque correcto ya es conocido
- Modificaciones de alcance limitado (función única, un archivo)

---

### El Patrón Híbrido (Plan-Luego-Ejecutar)

1. Explorar y diseñar en modo plan
2. Implementar usando ejecución directa con estrategia ya determinada

---

### El Subagente Explore

Aísla la salida verbosa de descubrimiento, manteniendo el contexto de la conversación principal limpio para el trabajo de implementación enfocado.

---

## Marco de Decisión

| Tipo de Tarea | Modo Recomendado |
|---|---|
| Reestructuración arquitectural | Modo plan |
| Migración de librería (muchos archivos) | Modo plan, luego directo |
| Corrección de bug en un archivo (traza de stack clara) | Ejecución directa |
| Base de código desconocida, tarea abierta | Modo plan |

---

## Puntos Clave del Examen

- Las reglas `deny` tienen prioridad sobre las reglas `allow`
- `--allowedTools` es una **lista blanca** en tiempo de invocación
- Plan vs. directo = **ambigüedad**, no dificultad
- Modo plan → sin modificaciones de archivos — solo exploración segura
- El patrón híbrido: plan → directo es la mejor práctica de producción

---

## Trampas Comunes del Examen

- Usar ejecución directa para cambios arquitecturales multi-archivo (necesita modo plan)
- Sobre-planificar correcciones simples de un solo archivo con requisitos claros
- Omitir el patrón híbrido plan-luego-ejecutar
- Tratar la dificultad de la tarea como el factor decisivo (es la ambigüedad lo que importa)

# Herramientas Integradas de Claude Code

**Peso en el examen:** Dominio 2 – Diseño de Herramientas e Integración MCP (18%)

---

## La Distinción Más Importante

> **Grep busca CONTENIDO de archivos. Glob coincide con RUTAS de archivos.**

Esta es la distinción más importante para el éxito en el examen.

---

## Grep

Localiza **texto dentro de archivos** — llamadas a funciones, mensajes de error, declaraciones de importación, asignaciones de variables.

**Usar para:**
- Encontrar todos los que llaman a `processLegacyOrder()`
- Encontrar archivos que importan un módulo específico
- Localizar mensajes de error en código fuente
- Buscar asignaciones de variables

---

## Glob

Encuentra **archivos por nombre, extensión o estructura de directorio**.

**Usar para:**
- Localizar archivos de prueba: `**/*.test.tsx`
- Encontrar archivos de configuración: `**/config.*`
- Listar todos los archivos TypeScript: `**/*.ts`
- Encontrar archivos en directorios específicos

---

## Herramientas de Modificación de Archivos

### Edit (Predeterminada)
Realiza reemplazos dirigidos usando coincidencia de texto único. Más rápida y usa menos contexto que Read + Write.

**Cuando el texto aparece múltiples veces:** Ampliar `old_string` con más contexto circundante para hacerlo único, o usar `replace_all: true`.

### Read + Write
Reservada para situaciones donde Edit no puede desambiguar la ubicación objetivo.

**Anti-patrón:** Leer todos los archivos por adelantado antes de entender la relevancia — "el mayor anti-patrón en la exploración de bases de código".

---

## Flujo de Trabajo de Exploración Recomendado

1. **Grep** para identificar puntos de entrada relevantes
2. **Read** archivos específicos para entender la estructura
3. **Grep** nuevamente para rastrear patrones de uso
4. **Read** solo archivos justificados por descubrimientos previos

Descubrimiento incremental — nunca leer todo por adelantado.

---

## Puntos Clave del Examen

- Grep = buscar **contenido** de archivos; Glob = coincidir **rutas** de archivos
- **Edit** es la predeterminada para modificaciones — más rápida, menos contexto
- Read + Write solo cuando Edit no puede localizar el objetivo
- Nunca leer todos los archivos antes de entender qué es relevante
- `replace_all: true` maneja objetivos de Edit ambiguos

---

## Trampas Comunes del Examen

- Usar Glob para encontrar quienes llaman a funciones (ese es trabajo de Grep)
- Usar Grep para búsquedas de archivos basadas en rutas (ese es trabajo de Glob)
- Usar Read + Write para todas las modificaciones (usar Edit primero)
- Leer todos los archivos fuente antes de entender la relevancia

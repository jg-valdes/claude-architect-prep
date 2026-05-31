# Ingeniería de Prompts

**Peso en el examen:** Dominio 4 – Ingeniería de Prompts y Salida Estructurada (20%)

---

## Prompts del Sistema con Criterios Explícitos

Las instrucciones vagas fallan en producción porque carecen de límites de decisión accionables.

### Inefectivo vs. Efectivo

| Inefectivo | Efectivo |
|---|---|
| "Sé conservador" | "Marca comentarios solo cuando el comportamiento afirmado contradiga el comportamiento real del código" |
| "Usa tu mejor juicio" | "Reporta bugs y vulnerabilidades de seguridad. Omite preferencias menores de estilo." |
| "Marca problemas críticos" | Mostrar patrones de código reales (ejemplo de inyección SQL) que ejemplifiquen "Crítico" |

**Regla:** Reemplazar adjetivos vagos con criterios categóricos concretos.

---

## El Problema de Confianza por Falsos Positivos

Las tasas altas de falsos positivos en una categoría erosionan la confianza **en todas las categorías**.

**Solución:** Deshabilitar temporalmente las categorías problemáticas mientras se refinan sus criterios. No intentar iterar en todas las categorías simultáneamente.

---

## Calibración de Severidad

Los ejemplos de código concretos superan a las descripciones en prosa. En lugar de definir "Crítico" como "problemas que causan fallos del sistema", mostrar patrones de código reales que ejemplifiquen cada nivel de severidad.

---

## Filtrado Basado en Confianza

La confianza auto-reportada de los LLMs está **mal calibrada**. Usar la jerarquía:
1. Criterios explícitos primero
2. Enrutamiento basado en confianza solo para filtrado secundario
3. Nunca como sustituto de definiciones claras

---

## Encadenamiento de Prompts

Dividir tareas complejas en pasos secuenciales donde la salida de cada paso alimenta al siguiente.

**Cuándo usar:** Cuando un solo prompt requeriría demasiado razonamiento en una pasada, o cuando los resultados intermedios necesitan validación antes de continuar.

---

## Bucles de Validación-Reintento

### Requisitos de Reintento Efectivo

Se requieren los tres componentes:

1. El documento fuente original (para re-examinación)
2. La salida de extracción fallida (para comparación)
3. **Detalles específicos del error de validación** — ej., "Los artículos de línea suman £450 pero el total declarado es £500"

Sin especificidad del error, los modelos reproducen errores idénticos — carecen de orientación sobre qué corregir.

---

## Qué Pueden y No Pueden Solucionar los Reintentos

| Corregible con reintento | No corregible con reintento |
|---|---|
| Discrepancias de formato (formatos de fecha, moneda) | Información ausente de los documentos fuente |
| Errores estructurales de salida | Datos que requieren documentos externos |
| Valores mal colocados | Campos que requieren conocimiento que el modelo no tiene |
| Errores matemáticos (artículos de línea omitidos) | — |

**Errores no corregibles → señalar para revisión humana**, no otro reintento.

---

## Diseño de Esquema de Auto-Corrección

Incrustar señales de validación en el esquema de extracción:

- `total_calculado` vs `total_declarado` — extraer ambos; la divergencia señala discrepancias automáticamente
- Boolean `conflicto_detectado` — señala información contradictoria dentro de los documentos fuente
- Campos `patron_detectado` — rastrean qué constructos dispararon los hallazgos

---

## Puntos Clave del Examen

- Criterios explícitos > adjetivos vagos — siempre
- Los umbrales de confianza no resuelven problemas de falsos positivos — corregir los criterios
- Los bucles de reintento requieren detalles específicos del error, no solo "inténtalo de nuevo"
- No todos los errores son reintentables — saber cuándo escalar a humanos
- La validación de esquema elimina errores de sintaxis pero NO errores semánticos

---

## Trampas Comunes del Examen

- Seleccionar mejoras vagas ("ser más preciso") como soluciones válidas
- Asumir que los umbrales de confianza resuelven problemas de falsos positivos
- Implementar reintentos sin incluir errores específicos de validación
- Mantener todas las categorías de revisión mientras se itera en una problemática

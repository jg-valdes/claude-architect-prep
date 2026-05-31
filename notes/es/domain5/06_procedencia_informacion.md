# Procedencia de la Información y Síntesis Multi-Fuente

**Peso en el examen:** Dominio 5 – Gestión de Contexto y Fiabilidad (15%)

---

## El Desafío Central

> "La atribución muere durante la resumarización."

Los agentes de síntesis comprimen y parafrasean sin instrucciones explícitas para preservar los mapeos de afirmación-fuente.

---

## Cinco Elementos Requeridos por Afirmación

Cada hallazgo en una síntesis multi-fuente debe incluir:

1. **Afirmación** — la aserción específica
2. **URL de fuente** — dónde se encontró la información
3. **Nombre del documento** — título de la fuente
4. **Extracto relevante** — pasaje de soporte
5. **Fecha de publicación** — cuándo fue publicado o recopilado el dato

---

## Manejo de Conflictos

Cuando las fuentes reportan estadísticas diferentes: **anotar ambos valores con atribución completa y fechas de publicación** — no seleccionar uno arbitrariamente.

Esto preserva la información y permite a los consumidores decidir basándose en el contexto.

---

## Conciencia Temporal

Diferentes fechas de publicación explican números diferentes:
- Fuente 2023: crecimiento del 8%
- Fuente 2024: crecimiento del 12%

Esto puede indicar **aceleración**, no contradicción. Las fechas deben acompañar todos los datos.

---

## Renderizado Apropiado al Contenido

| Tipo de Contenido | Renderizado |
|---|---|
| Datos financieros | Tablas |
| Noticias | Párrafos en prosa |
| Hallazgos técnicos | Listas estructuradas |

El formato uniforme degrada la comprensión en diferentes tipos de contenido.

---

## Requisito de Pipeline Multi-Paso

La atribución debe sobrevivir cada etapa:

```
Subagentes de investigación → salida de mapeos estructurados
        ↓
Agentes de análisis → preservar mapeos de afirmación-fuente
        ↓
Agentes de síntesis → fusionar manteniendo mapeos
        ↓
Informes finales → incluir citas en línea
```

---

## Puntos Clave del Examen

- La atribución debe preservarse explícitamente en cada etapa del pipeline — no sobrevive por defecto
- Fuentes conflictivas → anotar ambas, no elegir una
- Las diferencias temporales explican diferencias de datos — no llamarlo contradicción
- El formato apropiado al contenido es requerido — sin formato uniforme
- Pasar metadatos estructurados (no solo contenido) a los agentes posteriores

---

## Trampas Comunes del Examen

- Seleccionar la fuente más reciente cuando existen conflictos (en lugar de anotar ambas)
- Tratar números diferentes de fechas diferentes como contradicciones
- Permitir que la síntesis parafrasee sin preservar los mapeos de afirmación-fuente
- Renderizar toda la salida en formato uniforme independientemente del tipo de contenido

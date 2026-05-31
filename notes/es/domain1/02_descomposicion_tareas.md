# Descomposición de Tareas

**Peso en el examen:** Dominio 1 – Arquitectura Agéntica y Orquestación (27%)

---

## Dos Patrones de Descomposición

### Patrón 1: Pipelines Secuenciales Fijos (Encadenamiento de Prompts)

El trabajo se ejecuta a través de **pasos predeterminados en orden**. La salida de cada paso alimenta al siguiente. La secuencia nunca cambia independientemente de los resultados intermedios.

**Ejemplo:** Pipeline de revisión de código → análisis local por archivo → verificación de integración entre archivos → informe unificado.

**Fortalezas:** Consistencia, fiabilidad, depurabilidad.

**Ideal para:** Tareas predecibles y estructuradas — revisiones de código, procesamiento de documentos, extracción de datos, verificaciones de cumplimiento.

---

### Patrón 2: Descomposición Dinámica Adaptativa

Los agentes comienzan con un objetivo de alto nivel, investigan y **generan planes basados en descubrimientos**. Los planes evolucionan a medida que emerge nueva información.

**Ejemplo:** Agregar pruebas a una base de código heredada — mapear estructura → identificar áreas de alto impacto → crear plan de pruebas → adaptar a medida que emergen dependencias.

**Fortalezas:** Adaptabilidad y exhaustividad para problemas abiertos.

**Ideal para:** Tareas abiertas — exploración de sistemas heredados, auditorías de seguridad, depuración de código desconocido.

---

## Matriz de Selección

| Características de la Tarea | Patrón | Justificación |
|---|---|---|
| Pasos conocidos, entrada estructurada | Pipeline fijo | La fiabilidad importa más que la adaptabilidad |
| Alcance abierto, desconocido | Descomposición dinámica | Adaptabilidad esencial cuando el problema no está definido |
| Revisión de código multi-archivo | Pipeline fijo | Predecible: por archivo + entre archivos |
| Exploración de sistema heredado | Descomposición dinámica | Los problemas emergen durante la investigación |
| Extracción de documentos | Pipeline fijo | Campos y formato predeterminados |
| Depuración de sistemas desconocidos | Descomposición dinámica | Causa raíz desconocida |

---

## El Problema de Dilución de Atención

**Definición:** Procesar demasiados elementos en una sola pasada produce **profundidad de análisis inconsistente**.

**Síntomas:**
- Retroalimentación detallada para los archivos 1–5, cada vez más superficial para los archivos 10–14
- El mismo patrón marcado como ineficiente en Archivo 3, aprobado en Archivo 11
- Errores obvios pasados por alto en algunos archivos mientras se detectan problemas menores en otros

**Causa raíz:** El modelo distribuye atención entre todos los elementos. Cuando los elementos superan la capacidad, la atención por elemento disminuye.

**Solución:** Arquitectura multi-pasada:
1. **Pasadas de análisis local por elemento** — cada archivo analizado individualmente con presupuesto de atención completo
2. **Pasada de integración entre archivos** — análisis separado verificando flujo de datos, consistencia de API, uso de patrones

---

## Trampas Comunes del Examen

| Trampa | Por qué es Incorrecta |
|---|---|
| Sugerir un modelo mejor o ventana de contexto más grande | La dilución de atención es arquitectural, no una limitación del modelo |
| Proponer mejores prompts | Los prompts mejoran la calidad promedio pero no resuelven la asignación fundamental de atención |
| Agrupar archivos sin una pasada entre archivos | Omite problemas entre lotes como problemas de flujo de datos |
| Aplicar pipelines fijos a investigaciones abiertas | Patrón equivocado — necesita descomposición dinámica |

---

## Escenario de Práctica

> Un agente de revisión de código produce retroalimentación detallada para los primeros 5 archivos pero omite errores obvios en los archivos 10–14. Marca un bucle forEach como ineficiente en un archivo mientras aprueba código idéntico en otro.

**Respuesta correcta:** Dividir en pasadas de análisis local por archivo más una pasada de integración entre archivos separada para evitar la dilución de atención. **No** una actualización del modelo ni un aumento de la ventana de contexto.

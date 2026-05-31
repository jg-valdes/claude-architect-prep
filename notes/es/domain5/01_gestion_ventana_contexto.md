# Gestión de la Ventana de Contexto

**Peso en el examen:** Dominio 5 – Gestión de Contexto y Fiabilidad (15%)

---

## Los Cuatro Patrones Principales

### 1. La Trampa de la Resumarización Progresiva

Resumir conversaciones largas **destruye datos transaccionales críticos**.

**Ejemplo:** "El cliente quiere un reembolso de $247.83 por el pedido #8891 realizado el 3 de marzo" → "El cliente quiere un reembolso por un pedido reciente" después de la resumarización.

**Solución: Bloque de Hechos del Caso Persistente**
Extraer hechos transaccionales en un bloque estructurado incluido en **cada prompt pero nunca resumarizado**:

```json
{
  "hechos_caso": {
    "id_cliente": "C-88291",
    "id_pedido": "PED-8891",
    "monto_reembolso": 247.83,
    "fecha_pedido": "2024-03-03",
    "estado": "revision_pendiente"
  }
}
```

> El bloque de hechos del caso persistente es el **patrón más importante** en la gestión de contexto.

---

### 2. El Efecto "Perdido en el Medio"

Los modelos procesan información al **inicio y al final** de los documentos de forma confiable. Los hallazgos enterrados en el medio reciben menos atención.

Esto es estructural, no un problema de ingeniería de prompts.

**Solución:** Colocar resúmenes de hallazgos clave **al inicio** de las entradas agregadas con encabezados de sección explícitos. Resultados detallados debajo del resumen.

---

### 3. Recorte de Resultados de Herramientas

Las búsquedas de pedidos que devuelven 40+ campos (timestamps de auditoría, códigos de almacén, IDs de transportista) consumen tokens en cada turno posterior cuando solo se necesitan 5 campos.

**Solución:** Recortar los resultados de herramientas a campos relevantes **antes** de que entren al historial de conversación.

```python
def recortar_resultado_pedido(raw):
    return {
        "id_pedido": raw["id_pedido"],
        "estado": raw["estado"],
        "monto": raw["monto"],
        "nombre_cliente": raw["nombre_cliente"],
        "creado_en": raw["creado_en"]
    }
```

Reduce el exceso de tokens en un 80–90%.

---

### 4. Historial Completo de Conversación

La API de Claude no tiene estado — cada solicitud requiere el historial completo de conversación para la coherencia. No existe sesión del lado del servidor.

**No** truncar el historial selectivamente — rompe la comprensión conversacional.

**Sí:** Usar bloques de hechos del caso y resumarización para eficiencia mientras se mantiene el historial completo.

---

## Puntos Clave del Examen

- Los bloques de hechos del caso persistentes = **nunca resumarizados**, siempre incluidos en cada prompt
- "Perdido en el medio" = problema estructural de atención, no solucionable por prompts
- El recorte de resultados de herramientas reduce el exceso de contexto en un 80–90%
- El historial completo es requerido — no truncar selectivamente

---

## Trampas Comunes del Examen

- Asumir que la resumarización progresiva es segura para datos transaccionales (destruye la precisión)
- Creer que los recordatorios en prompts solucionan el efecto de perdido en el medio (es estructural)
- Mantener resultados de herramientas no recortados "por si se necesitan después"
- Intentar la truncación selectiva del historial de conversación

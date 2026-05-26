# Diseño de Esquema de Herramientas

**Peso en el examen:** Dominio 2 – Diseño de Herramientas e Integración MCP (18%)

---

## Por Qué Importa el Diseño de Esquema

Claude decide **cuándo** llamar una herramienta y **qué argumentos pasar** basándose enteramente en:
1. El **nombre** de la herramienta
2. La **descripción** de la herramienta
3. El **esquema de entrada** de la herramienta

Un esquema mal diseñado lleva a Claude a llamar la herramienta equivocada, pasar argumentos incorrectos o no llamar una herramienta cuando debería. El buen diseño de esquema es la palanca principal para la fiabilidad de las herramientas.

---

## Anatomía de una Definición de Herramienta

```json
{
  "name": "obtener_pedidos_cliente",
  "description": "Recupera todos los pedidos de un cliente específico. Úsalo cuando necesites consultar el historial de pedidos, verificar el estado de un pedido o encontrar un pedido específico para un cliente.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "id_cliente": {
        "type": "string",
        "description": "El identificador único del cliente (ej. 'CUST-12345')"
      },
      "estado": {
        "type": "string",
        "enum": ["pendiente", "enviado", "entregado", "cancelado"],
        "description": "Filtrar pedidos por estado. Omitir para devolver todos los pedidos."
      },
      "limite": {
        "type": "integer",
        "description": "Número máximo de pedidos a devolver. Por defecto 20.",
        "default": 20,
        "minimum": 1,
        "maximum": 100
      }
    },
    "required": ["id_cliente"]
  }
}
```

---

## Reglas para Buenas Descripciones de Herramientas

### Sé específico sobre cuándo usar la herramienta
```
Mal:  "Obtiene datos de clientes"
Bien: "Recupera todos los pedidos de un cliente específico. Úsalo cuando 
       necesites el historial de pedidos, estado de pedidos o encontrar 
       un pedido específico."
```

### Distingue entre herramientas similares
```
buscar_productos:      "Busca productos por palabra clave, categoría o 
                        rango de precio. Úsalo cuando no tengas un ID 
                        específico de producto."

obtener_producto_por_id: "Recupera un único producto por su ID exacto.
                          Úsalo cuando tengas el ID del producto y 
                          necesites detalles completos."
```

### Incluye pistas de formato en las descripciones de parámetros
```
Mal:  "id_cliente": { "description": "El ID del cliente" }
Bien: "id_cliente": { "description": "El identificador único del cliente (ej. 'CUST-12345')" }
```

---

## Mejores Prácticas de Esquema de Entrada

### Marca correctamente los campos requeridos vs opcionales
- `"required": ["campo1", "campo2"]` — solo incluye campos verdaderamente obligatorios
- Los campos opcionales deben tener valores por defecto sensatos

### Usa enums para valores con restricciones
```json
"estado": {
  "type": "string",
  "enum": ["activo", "inactivo", "pendiente"]
}
```
Esto evita que Claude pase valores inválidos y le ayuda a entender las opciones válidas.

### Usa tipos específicos, no solo `string`
```
Mal:  "monto": { "type": "string" }
Bien: "monto": { "type": "number", "minimum": 0 }
```

### Evita herramientas demasiado amplias
Una herramienta que hace 5 cosas diferentes basada en un parámetro `modo` es difícil de usar correctamente. Divídela en 5 herramientas enfocadas.

---

## Diseño de Límites de Herramientas

**Una herramienta = una responsabilidad.** Cada herramienta debe hacer exactamente una cosa claramente.

```
Mal:  gestionar_usuario(accion: "crear" | "actualizar" | "eliminar", ...)
Bien: crear_usuario(...), actualizar_usuario(...), eliminar_usuario(...)
```

Beneficios:
- Claude sabe exactamente qué herramienta llamar para cada tarea
- El esquema de cada herramienta puede optimizarse para sus entradas específicas
- Más fácil añadir permisos/restricciones por herramienta

---

## Puntos Clave para el Examen

- **Las descripciones de herramientas guían el comportamiento de Claude** — importan más que el código detrás de la herramienta
- Los **campos requeridos** solo deben incluir lo verdaderamente obligatorio
- Los **enums** restringen las entradas y ayudan a Claude a entender los valores válidos
- **Una herramienta, una responsabilidad** — evita herramientas multi-modo
- **Distingue claramente herramientas similares** en sus descripciones

---

## Trampa Común en el Examen

> "Claude sigue pasando valores de estado inválidos a una herramienta. ¿Cuál es la corrección más efectiva?"

Respuesta: Añade una restricción **enum** al campo `estado` en el esquema de entrada. Esto tanto previene valores inválidos como le dice explícitamente a Claude qué valores son válidos. Arreglarlo solo en la descripción es menos fiable.

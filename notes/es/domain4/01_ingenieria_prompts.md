# Ingeniería de Prompts y Salida Estructurada

**Peso en el examen:** Dominio 4 – Ingeniería de Prompts y Salida Estructurada (20%)

---

## Principios Principales de Ingeniería de Prompts

### 1. Sé Explícito, No Implícito
Claude hace lo que le pides — no lo que quisiste decir. Enuncia los requisitos directamente.

```
Mal:  "Resume este artículo bien"
Bien: "Resume este artículo en 3 puntos. Cada punto debe tener menos de 
       20 palabras. Enfócate en hechos clave, no en opiniones."
```

### 2. Usa Etiquetas XML para Estructurar
Las etiquetas XML ayudan a Claude a analizar prompts complejos y reducen confusión entre secciones:

```xml
<tarea>
  Clasifica la queja del cliente a continuación en una de las categorías.
</tarea>

<categorias>
  - facturacion
  - soporte_tecnico
  - envio
  - devoluciones
</categorias>

<queja>
  Mi paquete llegó dañado y quiero un reembolso.
</queja>
```

### 3. Coloca las Instrucciones Antes del Contenido
Claude procesa los prompts de arriba hacia abajo. Las instrucciones al inicio establecen el marco antes de que se lea el contenido.

```
Bien:
  "Traduce el siguiente texto al inglés. Solo escribe la traducción, 
   sin explicaciones."
  [texto a traducir]

Mal:
  [texto a traducir]
  "Traduce lo anterior al inglés."
```

### 4. Especifica el Formato de Salida Explícitamente
Nunca asumas que Claude usará el formato que quieres. Indícalo:

```
"Responde con un objeto JSON. Sin bloques de código markdown, sin 
 explicaciones — solo el JSON crudo."
```

---

## Prompting Few-Shot

El prompting few-shot le da a Claude ejemplos del patrón entrada/salida que deseas antes de pedirle que maneje la entrada real.

### Estructura
```
[Instrucción]

[Ejemplo 1 entrada]
[Ejemplo 1 salida]

[Ejemplo 2 entrada]
[Ejemplo 2 salida]

[Entrada real]
```

### Ejemplo
```
Clasifica cada mensaje de cliente como "urgente" o "normal".

Mensaje: "¡Mi cuenta fue hackeada y no puedo entrar!"
Clasificación: urgente

Mensaje: "¿Puedes explicar cómo actualizar mi dirección de facturación?"
Clasificación: normal

Mensaje: "¡Me cobraron dos veces por el mismo pedido!"
Clasificación: urgente

Mensaje: "¿Dónde puedo encontrar mi historial de facturas?"
Clasificación:
```

### Reglas Clave para Few-Shot
- Los ejemplos van **antes** de la entrada real, nunca después
- Usa **2–5 ejemplos** — más rara vez es mejor
- Los ejemplos deben **cubrir casos límite**, no solo casos fáciles
- Todos los ejemplos deben usar el **mismo formato** que esperas en la salida
- Los ejemplos deben ser **representativos** de entradas reales

---

## Salida Estructurada: Aplicación de Esquema JSON

Cuando necesitas que Claude produzca datos estructurados (JSON), aplícalo en múltiples niveles:

### Nivel 1 — Instrucción en el prompt
```
"Produce un objeto JSON con estos campos exactos:
 - nombre (string)
 - puntuacion (entero, 0-100)
 - razon (string, máx. 50 palabras)
 
 Solo JSON válido. Sin markdown, sin explicaciones."
```

### Nivel 2 — Esquema en el prompt
```
La salida debe coincidir con este esquema:
{
  "nombre": "string",
  "puntuacion": "entero entre 0 y 100",
  "razon": "string"
}
```

### Nivel 3 — Aplicación a nivel de API
Usa la función `tool_use` o el modo de salida estructurada en la API — Claude está forzado a producir salida que coincida con tu esquema JSON a nivel de API. La opción más fiable cuando está disponible.

---

## Bucles de Validación y Reintento

Nunca asumas que la salida estructurada es válida al primer intento. Siempre valida y reintenta en caso de fallo:

```python
for intento in range(MAX_REINTENTOS):
    respuesta = claude.llamar(prompt)
    
    try:
        datos = json.loads(respuesta)
        validar_esquema(datos)
        return datos             # éxito
    except (json.JSONDecodeError, ErrorValidacion) as e:
        if intento == MAX_REINTENTOS - 1:
            raise
        prompt = ajustar_prompt(prompt, error=str(e))  # pasar el error a Claude
```

### Ajustar el Prompt en el Reintento
No reintentes con el mismo prompt — incluye el error:

```
"Tu respuesta anterior no era JSON válido. Error: faltaba el campo 
 'puntuacion'. Por favor intenta de nuevo e incluye todos los campos 
 requeridos."
```

---

## Posicionamiento del Contexto

Dónde colocas la información en el prompt afecta cómo Claude la pondera:

| Posición | Efecto |
|---|---|
| Prompt del sistema | Reglas persistentes, persona, restricciones |
| Inicio del mensaje de usuario | Marco de la tarea, instrucción de alto nivel |
| Medio | Contexto de apoyo, datos |
| Final del mensaje de usuario | Instrucción final / qué hacer ahora |

**Sesgo de recencia:** Las instrucciones cerca del final del prompt tienden a seguirse con más fiabilidad — útil para reforzar requisitos críticos de formato de salida.

---

## Puntos Clave para el Examen

- Las **etiquetas XML** son la forma preferida de estructurar prompts complejos con Claude
- Los **ejemplos few-shot van antes** de la entrada real, nunca después
- La **validación + reintento** es obligatoria para salida estructurada en sistemas de producción
- **Incluye el mensaje de error** al reintentar — "inténtalo de nuevo" sin contexto rara vez ayuda
- La **aplicación a nivel de API** (tool_use / salida estructurada) es más fiable que solo el prompt
- **Sesgo de recencia** — repite instrucciones críticas de formato al final del prompt

---

## Trampa Común en el Examen

> "Claude ocasionalmente produce JSON envuelto en bloques de código markdown a pesar de que se le indica que no lo haga. ¿Cuál es la corrección más fiable?"

Respuesta: Usa **aplicación de salida estructurada a nivel de API** (tool_use o modo de salida estructurada) — fuerza JSON válido a nivel de API, evitando la tendencia del modelo a añadir formato. Repetir la instrucción en el prompt ayuda pero es menos fiable.

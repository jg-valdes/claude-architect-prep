# Patrones de Salida Estructurada

**Peso en el examen:** Dominio 4 – Ingeniería de Prompts y Salida Estructurada (20%)

---

## ¿Por Qué Salida Estructurada?

Los sistemas agénticos necesitan pasar datos entre pasos de forma programática. La salida en lenguaje natural requiere análisis — la salida estructurada (JSON, XML) puede consumirse directamente por código. La salida estructurada fiable es un requisito previo para pipelines agénticos fiables.

---

## Las Tres Capas de Aplicación

Siempre piensa en capas — cada una añade fiabilidad:

```
Capa 3 (más fuerte): Aplicación a nivel de API (tool_use / modo de salida estructurada)
Capa 2 (media):      Esquema en el prompt + instrucción de formato explícita
Capa 1 (más débil):  Solo instrucción ("produce JSON")
```

Usa la capa más fuerte disponible para sistemas de producción.

---

## Capa 1: Solo Instrucción en el Prompt

```
"Responde con un objeto JSON."
```

Fiabilidad: **Baja** — Claude puede añadir bloques markdown, texto explicativo o desviarse del esquema bajo razonamiento complejo.

Uso: Solo para prototipado rápido.

---

## Capa 2: Esquema + Instrucción en el Prompt

Proporciona el esquema exacto y repite la instrucción de formato al final:

```
Analiza el sentimiento de la reseña a continuación.

Produce un objeto JSON que coincida exactamente con este esquema:
{
  "sentimiento": "positivo" | "negativo" | "neutral",
  "confianza": número entre 0.0 y 1.0,
  "frases_clave": array de strings (máx. 3)
}

Reglas:
- Solo el objeto JSON
- Sin bloques de código markdown
- Sin explicación antes ni después

Reseña: "El producto funciona genial pero el envío tardó muchísimo."

Produce el JSON ahora:
```

Fiabilidad: **Media** — mucho mejor que solo instrucción. Falla ocasionalmente en entradas complejas.

---

## Capa 3: Aplicación a Nivel de API

### Patrón Tool Use
Define la estructura de salida como una "herramienta" que Claude debe llamar. Claude está forzado a producir argumentos que coincidan con tu esquema JSON:

```python
herramientas = [{
    "name": "registrar_sentimiento",
    "description": "Registra el resultado del análisis de sentimiento",
    "input_schema": {
        "type": "object",
        "properties": {
            "sentimiento": {
                "type": "string",
                "enum": ["positivo", "negativo", "neutral"]
            },
            "confianza": {
                "type": "number",
                "minimum": 0.0,
                "maximum": 1.0
            },
            "frases_clave": {
                "type": "array",
                "items": {"type": "string"},
                "maxItems": 3
            }
        },
        "required": ["sentimiento", "confianza", "frases_clave"]
    }
}]

respuesta = anthropic.messages.create(
    model="claude-sonnet-4-6",
    tools=herramientas,
    tool_choice={"type": "auto"},
    messages=[{"role": "user", "content": prompt}]
)
```

Fiabilidad: **Alta** — el esquema se aplica a nivel de API, no solo en el prompt.

---

## Patrones de Validación

### Validación de Esquema
Después de recibir la salida, valídala contra tu esquema esperado antes de usarla:

```python
import jsonschema

esquema = {
    "type": "object",
    "required": ["sentimiento", "confianza"],
    "properties": {
        "sentimiento": {"type": "string", "enum": ["positivo", "negativo", "neutral"]},
        "confianza": {"type": "number", "minimum": 0, "maximum": 1}
    }
}

jsonschema.validate(salida, esquema)  # lanza ValidationError si es inválido
```

### Validación de Lógica de Negocio
La validez del esquema no significa que la salida tenga sentido:

```python
# Válido en esquema pero lógicamente incorrecto:
{"sentimiento": "positivo", "confianza": 0.02, "frases_clave": ["terrible", "pésimo"]}

# Añade verificaciones de lógica de negocio:
if salida["sentimiento"] == "positivo" and salida["confianza"] < 0.5:
    raise ValueError("Sentimiento positivo con baja confianza — necesita revisión")
```

---

## Patrón de Bucle de Reintento (Listo para Producción)

```python
MAX_REINTENTOS = 3

def obtener_salida_estructurada(prompt, esquema):
    ultimo_error = None
    
    for intento in range(MAX_REINTENTOS):
        contexto_reintento = ""
        if ultimo_error:
            contexto_reintento = f"\n\nTu respuesta anterior falló la validación: {ultimo_error}\nPor favor corrígelo e inténtalo de nuevo."
        
        respuesta = claude.llamar(prompt + contexto_reintento)
        
        try:
            datos = json.loads(respuesta)
            jsonschema.validate(datos, esquema)
            return datos  # éxito
        except Exception as e:
            ultimo_error = str(e)
    
    raise RuntimeError(f"Falló después de {MAX_REINTENTOS} intentos. Último error: {ultimo_error}")
```

Puntos clave:
- Siempre incluye el **error específico** en el prompt de reintento
- Limita los reintentos — 3 generalmente es suficiente
- Lanza excepción después de agotar reintentos — no devuelvas datos incorrectos silenciosamente

---

## Puntos Clave para el Examen

- **Tres capas de aplicación** — siempre usa la más fuerte disponible para producción
- **Tool use** es la forma más fiable de aplicar salida estructurada a nivel de API
- **Valida después de recibir** — nunca asumas que la salida es válida
- **Incluye el error en los reintentos** — "inténtalo de nuevo" genérico rara vez soluciona el problema
- La **validación de lógica de negocio** es separada de la validación de esquema — ambas son necesarias
- **Limita los reintentos** — define un máximo y lanza excepción al agotarlos

---

## Trampa Común en el Examen

> "Un paso del pipeline requiere que Claude produzca un array JSON de objetos. Claude ocasionalmente produce un objeto JSON en lugar de un array. ¿Cuál es la corrección correcta?"

Respuesta: Usa **aplicación a nivel de API mediante tool_use** con un esquema que especifique `"type": "array"` — esto previene el tipo incorrecto a nivel de API. Actualizar solo la instrucción del prompt es poco fiable para una aplicación consistente.

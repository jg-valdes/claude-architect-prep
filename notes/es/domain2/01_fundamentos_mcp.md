# Fundamentos de MCP

**Peso en el examen:** Dominio 2 – Diseño de Herramientas e Integración MCP (18%)

---

## ¿Qué Es MCP?

**Model Context Protocol (MCP)** es un protocolo abierto que estandariza cómo los modelos de IA se conectan a herramientas externas, fuentes de datos y servicios. Piénsalo como un adaptador universal — en lugar de construir una integración personalizada para cada herramienta, construyes un servidor MCP y cualquier cliente compatible con MCP (como Claude) puede usarlo.

```
Claude (Cliente MCP) ←→ Protocolo MCP ←→ Servidor MCP ←→ Herramienta/Datos externos
```

---

## Conceptos Principales

### Servidor MCP
- Expone **herramientas**, **recursos** y **prompts** al cliente
- Corre como un proceso separado (local) o servicio remoto
- Define qué capacidades están disponibles y cómo invocarlas
- Es responsable de ejecutar la lógica de la herramienta

### Cliente MCP
- El modelo de IA (Claude) o la aplicación anfitriona
- Descubre las herramientas disponibles desde el servidor
- Llama herramientas enviando solicitudes estructuradas
- Recibe respuestas estructuradas

### Herramientas
Funciones que el modelo puede invocar. Se definen con nombre, descripción y esquema de entrada:

```json
{
  "name": "buscar_base_datos",
  "description": "Busca en la base de datos de productos por palabra clave",
  "inputSchema": {
    "type": "object",
    "properties": {
      "consulta": { "type": "string", "description": "Palabra clave de búsqueda" },
      "limite": { "type": "integer", "description": "Máx. resultados", "default": 10 }
    },
    "required": ["consulta"]
  }
}
```

### Recursos
Datos estáticos o dinámicos que el servidor expone (archivos, registros de base de datos, respuestas de API). Los recursos son de solo lectura — a diferencia de las herramientas, no realizan acciones.

### Prompts
Plantillas de prompts reutilizables que el servidor expone al cliente. Menos comunes en la práctica.

---

## Los Dos Transportes

MCP admite dos mecanismos de transporte:

### stdio (Entrada/Salida Estándar)
- El cliente y el servidor se comunican mediante stdin/stdout
- El servidor corre como un **subproceso local**
- Simple de configurar — no requiere red
- Mejor para herramientas locales (acceso al sistema de archivos, bases de datos locales, herramientas CLI)

### SSE (Server-Sent Events)
- El cliente y el servidor se comunican mediante **HTTP**
- El servidor corre como un **servicio remoto**
- Admite múltiples clientes simultáneos
- Requiere red — introduce latencia y preocupaciones de autenticación
- Mejor para servicios compartidos, APIs remotas, herramientas en la nube

---

## Puntos Clave para el Examen

- MCP es un **protocolo**, no una biblioteca — define el estándar de comunicación
- Las **herramientas** realizan acciones; los **recursos** exponen datos de solo lectura
- **stdio** = subproceso local; **SSE** = servicio HTTP remoto
- La **descripción de la herramienta** es crítica — Claude la usa para decidir cuándo y cómo llamar la herramienta
- Los servidores MCP son **agnósticos al lenguaje** — pueden escribirse en Python, TypeScript, Go, etc.

---

## Trampa Común en el Examen

> "¿Cuál es el propósito principal de MCP?"

Respuesta: Proporcionar un **protocolo estandarizado** para conectar modelos de IA a herramientas externas y fuentes de datos — no mejorar el rendimiento del modelo, ni añadir memoria, ni habilitar sistemas multi-agente (aunque puede apoyar esos casos de uso).

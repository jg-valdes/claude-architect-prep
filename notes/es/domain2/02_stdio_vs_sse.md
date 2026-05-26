# Transporte stdio vs SSE

**Peso en el examen:** Dominio 2 – Diseño de Herramientas e Integración MCP (18%)

---

## Los Dos Transportes Comparados

| | stdio | SSE (Server-Sent Events) |
|---|---|---|
| **Dónde corre el servidor** | Subproceso local | Servidor HTTP remoto |
| **Comunicación** | stdin / stdout | HTTP long-polling |
| **Múltiples clientes** | No — un cliente por proceso | Sí — muchos clientes, un servidor |
| **Latencia** | Mínima (local) | Latencia de red |
| **Autenticación** | No necesaria (local) | Requerida (tokens, API keys) |
| **Complejidad de configuración** | Baja | Mayor |
| **Mejor para** | Herramientas locales, entornos de desarrollo | Servicios compartidos, herramientas en la nube |

---

## Transporte stdio

El servidor MCP se lanza como un **proceso hijo** por el cliente. Se comunican mediante streams de entrada/salida estándar.

```
Claude Code
  └── lanza → proceso Servidor MCP
                 ↕ mensajes JSON-RPC por stdin/stdout
```

**Cuándo usar stdio:**
- Herramientas del sistema de archivos (leer/escribir archivos locales)
- Conexiones a base de datos local
- Wrappers de herramientas CLI
- Desarrollo y pruebas
- Herramientas que solo necesita un usuario

**Ventajas:**
- Sin sobrecarga de red
- Sin necesidad de autenticación — aislamiento de procesos a nivel de SO
- Configuración simple — solo un comando para ejecutar
- Automáticamente con alcance de un usuario/sesión

**Desventajas:**
- No se puede compartir entre múltiples usuarios o máquinas
- Requiere que el servidor esté instalado localmente

---

## Transporte SSE

El servidor MCP corre como un servidor HTTP independiente. Los clientes se conectan mediante HTTP y reciben respuestas como Server-Sent Events.

```
Claude Code ──solicitud HTTP──→ Servidor MCP (remoto)
Claude Code ←──stream SSE────── Servidor MCP (remoto)
```

**Cuándo usar SSE:**
- Herramientas internas compartidas (múltiples miembros del equipo usan el mismo servidor)
- Fuentes de datos hospedadas en la nube
- APIs que necesitan gestión centralizada de autenticación
- Herramientas de producción de alta disponibilidad
- Entornos multi-inquilino

**Ventajas:**
- Compartido entre muchos clientes simultáneamente
- Gestionado centralmente — actualiza una vez, todos los clientes se benefician
- Se puede desplegar en infraestructura cloud

**Desventajas:**
- Requiere autenticación (tokens, OAuth, API keys)
- Latencia de red en cada llamada a herramienta
- Más complejo de configurar y asegurar
- Superficie de ataque adicional (endpoint HTTP expuesto)

---

## Autenticación en SSE

Dado que los servidores SSE están expuestos mediante HTTP, requieren autenticación:

**Patrones comunes:**
- **Bearer tokens** — el cliente envía el header `Authorization: Bearer <token>`
- **API keys** — el cliente envía la clave en el header o parámetro de consulta
- **OAuth 2.0** — flujo OAuth completo para acceso delegado por usuario
- **mTLS** — TLS mutuo para autenticación máquina a máquina

El servidor MCP es responsable de validar la autenticación en cada solicitud entrante.

---

## Elegir el Transporte Correcto

```
¿La herramienta solo se necesita en la máquina local?
  Sí → stdio

¿La herramienta se comparte entre un equipo o múltiples máquinas?
  Sí → SSE

¿Es para desarrollo/prototipado?
  Sí → stdio (más simple de configurar)

¿Es para producción con múltiples usuarios?
  Sí → SSE
```

---

## Puntos Clave para el Examen

- **stdio** = subproceso local, sin autenticación, un cliente — simple y rápido
- **SSE** = HTTP remoto, requiere autenticación, muchos clientes — escalable y compartible
- **La autenticación es solo una preocupación de SSE** — stdio no la necesita (el aislamiento del SO es suficiente)
- SSE introduce **latencia de red** — tenla en cuenta al diseñar herramientas sensibles a la latencia
- Ambos transportes usan el **mismo protocolo MCP** — solo difiere la capa de transporte

---

## Trampa Común en el Examen

> "Una empresa quiere dar a sus 50 desarrolladores acceso a una herramienta de base de datos interna compartida mediante MCP. ¿Qué transporte deben usar?"

Respuesta: **SSE** — es un servicio compartido con múltiples clientes. stdio solo admite un cliente por instancia de proceso y requiere instalación local en cada máquina.

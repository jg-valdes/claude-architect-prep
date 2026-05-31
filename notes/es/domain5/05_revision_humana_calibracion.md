# Revisión Humana y Calibración de Confianza

**Peso en el examen:** Dominio 5 – Gestión de Contexto y Fiabilidad (15%)

---

## La Trampa de las Métricas Agregadas

> "Un sistema reporta 97% de precisión general. El equipo celebra."

Esto oculta fallos catastróficos en tipos específicos de documentos. Un 97% agregado puede ocultar tasas de error del 40% en recibos manuscritos o formatos internacionales porque las facturas estándar dominan el volumen.

**Regla:** Validar la precisión por **tipo de documento Y segmento de campo por separado** — nunca confiar en métricas agregadas solas para decisiones de automatización.

---

## Calibración de Confianza

Los puntajes de confianza brutos del modelo carecen de significado absoluto. Un puntaje de confianza de 0.90 podría indicar:
- 94% de precisión real en fechas
- 82% de precisión real en montos

**La calibración requiere conjuntos de validación etiquetados** — ejemplos reales donde las respuestas correctas son conocidas. Construir curvas de calibración que mapeen confianza reportada a precisión real.

---

## Muestreo Aleatorio Estratificado

Debe incluir **extracciones de alta confianza**, no solo elementos de baja confianza.

Por qué: Detecta nuevos patrones de error que no existían en los conjuntos de validación y confirma el mantenimiento de precisión continuo en todos los segmentos.

---

## Priorización de Revisores

Enrutar **primero los elementos de mayor incertidumbre** en lugar de distribuir la capacidad de forma uniforme. Usar ordenamiento dinámico de colas para que los revisores siempre aborden las extracciones más inciertas a continuación.

---

## Secuencia de Implementación

1. Medir precisión por tipo de documento y campo
2. Calibrar confianza usando conjuntos de validación etiquetados
3. Establecer umbrales calibrados para enrutamiento de automatización
4. Implementar muestreo aleatorio estratificado
5. Solo entonces reducir la revisión humana en segmentos validados

---

## Puntos Clave del Examen

- La precisión agregada puede ocultar fallos a nivel de segmento — siempre segmentar la validación
- Los puntajes de confianza brutos no están calibrados — requieren conjuntos de validación etiquetados
- El muestreo debe incluir **elementos de alta confianza** (para detectar nuevos patrones)
- Enrutar primero los elementos de mayor incertidumbre
- Reducir la revisión humana **solo después** de que la calibración esté completa

---

## Trampas Comunes del Examen

- Usar precisión agregada para justificar automatización completa
- Muestrear solo extracciones de baja confianza (omite nuevos patrones de error en alta confianza)
- Desplegar puntajes de confianza brutos no calibrados para decisiones de enrutamiento
- Distribuir la capacidad del revisor uniformemente en lugar de priorizar los inciertos

---
name: value-driven-product-mapper
description: Protocolo para el diseño de la experiencia de usuario con IA, mapeando el flujo de consumo de predicciones y estableciendo criterios de aceptación funcionales que garanticen la utilidad operativa del producto.
user-invocable: false
agent: ai-business-strategist
allowed-tools: [Read, Write, Edit]
---

## 🏗️ I. Diseño de User Stories de IA (IA-UX)

A diferencia de las historias de usuario tradicionales, estas deben incluir el componente de incertidumbre de la IA:

1. **Estructura de la Historia:** "Como [Perfil de Usuario], quiero recibir [Predicción/Insight] con un [Nivel de Confianza] para poder ejecutar [Acción de Negocio] y ver el resultado en [Interfaz]."
2. **Gestión de la Incertidumbre:** Definir qué debe hacer la UI cuando el modelo tiene baja confianza (ej: "¿Desea revisar este caso manualmente?").
3. **Explicabilidad Necesaria (XAI):** Determinar si el usuario necesita saber el "por qué" (ej: "Se deniega el crédito porque la relación deuda-ingreso es > 40%").

## 📐 II. Definición de Criterios de Aceptación (DoD)

El agente debe redactar los puntos que el sistema debe cumplir para ser aprobado en la Phase Delivery:

* **Criterios de Performance:** "El sistema debe procesar la predicción en menos de $X$ milisegundos."
* **Criterios de Interfaz:** "El dashboard debe mostrar un mapa de calor con las variables que más influyeron en el resultado."
* **Criterios de Integración:** "La predicción debe enviarse automáticamente al CRM si la probabilidad de compra es > 85%."

## 🚀 III. Mapeo del Ciclo de Feedback

Protocolo para el aprendizaje continuo tras el despliegue:
1. **Captura de Reacción:** ¿Cómo sabremos si la predicción fue correcta en el mundo real? (Ground Truth de producción).
2. **Mecanismo de Corrección:** Diseñar el flujo para que el usuario humano corrija al modelo (ej: un botón de "Esta predicción es incorrecta"), lo cual alimentará futuros reentrenamientos en la Phase Modeling.

---

> **Check de Certificación de Producto:**
> - [ ] ¿La User Story define claramente la acción que sigue a la predicción?
> - [ ] ¿Se han establecido umbrales de decisión (thresholds) claros para el Backend?
> - [ ] ¿El diseño incluye visualizaciones que el cliente final pueda interpretar sin ser científico de datos?

---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

---
name: ask-me
description: Entrevista al usuario implacablemente sobre un plan o diseño hasta alcanzar un entendimiento compartido, resolviendo cada rama del árbol de decisión. Documenta cada pregunta y respuesta en el Shared Understanding Log.
user-invocable: false
agent: ai-business-strategist
allowed-tools: [Read, Write, Edit]
---

# Skill: ask-me (Protocolo de Interrogación Socrática)

Entrevístame implacablemente sobre cada aspecto de este plan hasta que alcancemos un entendimiento compartido (Shared Understanding). Recorre cada rama del árbol de diseño, resolviendo las dependencias entre las decisiones una por una. Para cada pregunta, proporciona siempre tu respuesta recomendada.

## Reglas Críticas de Ejecución:
1. **UNA SOLA PREGUNTA A LA VEZ:** Nunca debes hacer múltiples preguntas en un solo mensaje. Haz una, espera la respuesta del usuario, y luego formula la siguiente.
2. **Exploración Proactiva:** Si una pregunta puede ser respondida explorando el código base, explora el código en lugar de preguntar. No le preguntes al usuario lo que puedes descubrir tú mismo leyendo el código o la documentación.
3. **Registro Continuo:** Inmediatamente después de hacer una pregunta y de recibir la respuesta del usuario, DEBES registrar ambas (pregunta y respuesta) en el archivo `docs/Phase_discovery/shared_understanding.md`. Este documento debe ser la memoria inmutable de toda la sesión de "ask-me".


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

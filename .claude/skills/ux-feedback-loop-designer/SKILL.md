---
name: ux-feedback-loop-designer
description: Protocolo para la gestión del estado de la aplicación y la captura de feedback del usuario para la mejora continua del modelo.
user-invocable: false
agent: ai-frontend-engineer
allowed-tools: [Read, Write, Edit, Python-Interpreter]
---

## 🎨 0. Pre-Flight: Design System
Antes de diseñar botones, formularios o alertas de feedback:
1. **Verificar** si existe `docs/design-system/`.
2. **Si existe:** Leer `docs/design-system/DESIGN.md` sección "Components" para aplicar los estilos correctos a botones de acción (Continue/Change), alertas de estado y formularios de corrección. Los colores de "Acción Primaria" vs "Acción Secundaria" deben seguir la jerarquía definida por el cliente.
3. **Si no existe:** Preguntar — *"¿Tienes colores o estilo definido para botones de acción y alertas? Si no, aplicaré convenciones estándar de Streamlit."* Crear `docs/design-system/DESIGN.md` si el usuario responde, o continuar con defaults si omite.

## 🏗️ I. Captura de "Ground Truth" Humano
El agente debe diseñar los mecanismos para que el usuario aprenda del modelo y viceversa:
1. **Botón de Reporte de Error:** Implementar un flujo para que el usuario marque una predicción como incorrecta, enviando la metadata al MLOps.
2. **Formulario de Corrección:** Permitir que el usuario edite los datos sugeridos por el modelo (ej: corregir una categoría mal imputada).
3. **Encuestas de Utilidad:** Capturar si la predicción fue útil para la toma de decisiones del negocio.

## 📐 II. Gestión de Estado Global (App State)
1. **Persistencia Local:** Guardar preferencias del usuario y resultados recientes para agilizar la navegación.
2. **Sincronización con el Backend:** Asegurar que los cambios realizados por el usuario se guarden correctamente en la base de datos de auditoría.

## 🚀 III. Notificaciones de Eventos Predicitivos
1. **Alertas en Tiempo Real:** Implementar notificaciones (Push/Toast) cuando un proceso asíncrono finaliza o cuando el modelo detecta un caso crítico.

---

> **Check de Certificación de Feedback:**
> - [ ] ¿Existe un mecanismo claro para reportar discrepancias en el modelo?
> - [ ] ¿El feedback del usuario se está enviando al backend con el contexto completo?
> - [ ] ¿La navegación entre estados de la aplicación es fluida y coherente?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

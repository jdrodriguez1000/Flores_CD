---
name: interactive-dashboard-builder
description: Protocolo para el desarrollo de interfaces de usuario (Streamlit, React, Next.js) que permitan la interacción fluida con modelos de IA y visualización de datos.
user-invocable: false
agent: ai-frontend-engineer
allowed-tools: [Read, Write, Edit, Bash, Python-Interpreter]
---

## 🎨 0. Pre-Flight: Design System
Antes de escribir cualquier código de interfaz, ejecutar este protocolo agnóstico:

1. **Verificar** si existe `docs/design-system/` en el proyecto.
2. **Si existe:**
   - Leer `docs/design-system/DESIGN.md` → identificar paleta, tipografía, reglas de componentes y restricciones (Do's/Don'ts).
   - Leer `docs/design-system/code.html` → extraer el bloque `tailwind.config` con todos los tokens de color.
   - **Para Streamlit:** Traducir tokens al archivo `.streamlit/config.toml`:
     ```toml
     [theme]
     primaryColor             = "<primary>"
     backgroundColor          = "<surface>"
     secondaryBackgroundColor = "<surface-container-low>"
     textColor                = "<on-surface>"
     ```
   - **CSS adicional:** Inyectar fuentes, border-radius y reglas de componentes vía `st.markdown("<style>...</style>", unsafe_allow_html=True)`.
   - **Para React/Next.js:** Copiar el bloque `tailwind.config` directamente al `tailwind.config.js` del proyecto.
3. **Si no existe:** Preguntar al usuario antes de continuar:
   > "No encontré un Design System en `docs/design-system/`. ¿Deseas definir uno básico? Necesito:
   > - Color primario (hex)
   > - Color de fondo (hex)
   > - Color de texto (hex)
   > - Fuente principal
   >
   > Si prefieres omitirlo, usaré el tema estándar del framework."
   - **Usuario responde:** Crear `docs/design-system/DESIGN.md` con los tokens y aplicarlos al dashboard.
   - **Usuario omite:** Continuar con convenciones estándar del framework.

> El Design System es **inmutable**. No alterar colores, fuentes ni reglas de componentes salvo CC aprobado.

## 🏗️ I. Diseño de la Arquitectura de UI
El agente debe construir el punto de entrada para el usuario final:
1. **Flujo de Carga de Datos:** Implementar componentes para subir archivos (CSV/Excel) o formularios de entrada manual con validación visual inmediata.
2. **Visualización de Resultados:** Diseñar paneles que presenten las predicciones de forma clara, priorizando la información crítica (ej: Predict Score).
3. **Responsive Design:** Asegurar que el dashboard sea utilizable en diferentes dispositivos (Desktop/Tablet) según el requisito del cliente.

## 📐 II. Integración con el Backend
1. **Consumo de API:** Implementar la lógica para llamar a los endpoints de la Phase Delivery, manejando estados de carga (`loading`) y errores técnicos de forma elegante.
2. **Visualización de Logs de Proceso:** Mostrar al usuario el progreso de las tareas asíncronas (ej: "Limpiando datos...", "Calculando predicción...").

## 🚀 III. Prototipado Rápido vs. Producción
1. **Streamlit (Discovery):** Uso de componentes estándar para validación rápida con stakeholders.
2. **Next.js / Tailwind (Producción):** Implementación de interfaces de alta fidelidad, con micro-animaciones y diseño premium según el manual de marca.

---

> **Check de Certificación de UI:**
> - [ ] ¿La interfaz permite completar el flujo de predicción de punta a punta?
> - [ ] ¿Se manejan correctamente los errores de red y de validación del backend?
> - [ ] ¿El diseño es intuitivo para un usuario que no conoce de ciencia de datos?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

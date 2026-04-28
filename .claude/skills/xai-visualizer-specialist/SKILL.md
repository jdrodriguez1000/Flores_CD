---
name: xai-visualizer-specialist
description: Protocolo para la visualización de la explicabilidad del modelo (XAI) mediante componentes gráficos que faciliten la interpretación de predicciones.
user-invocable: false
agent: ai-frontend-engineer
allowed-tools: [Read, Write, Edit, Python-Interpreter]
---

## 🎨 0. Pre-Flight: Design System
Antes de definir cualquier paleta de colores para gráficos o componentes visuales:
1. **Verificar** si existe `docs/design-system/`.
2. **Si existe:** Leer `docs/design-system/DESIGN.md` y extraer los tokens de `code.html`. Usar `primary`, `tertiary-container` y `error` como paleta semántica para positivo/neutro/negativo en SHAP. Nunca usar rojo/verde arbitrarios si el Design System define colores de estado.
3. **Si no existe:** Preguntar — *"¿Tienes colores corporativos definidos para los gráficos? (color positivo, negativo, neutro). Si no, usaré la convención estándar verde/rojo."* Crear `docs/design-system/DESIGN.md` si el usuario responde, o continuar con defaults si omite.

## 🏗️ I. Representación Visual de SHAP/Importance
El agente debe traducir los números del Data Scientist en gráficos comprensibles:
1. **Barras de Contribución:** Mostrar qué variables empujaron la predicción hacia arriba o hacia abajo (ej: Rojo para negativo, Verde para positivo).
2. **Semáforos de Confianza:** Implementar indicadores visuales basados en la probabilidad del modelo (ej: Verde > 80%, Amarillo 50-80%, Rojo < 50%).
3. **Tooltips de Explicación:** Traducir nombres técnicos de variables (ej: `feat_income_log`) a lenguaje de negocio (ej: "Nivel de Ingresos Mensuales").

## 📐 II. Narrativa Predictiva
1. **Compresión de Complejidad:** Ocultar variables de baja importancia para evitar la sobrecarga cognitiva del usuario.
2. **Comparativa Local vs. Global:** Mostrar cómo se compara el caso actual con el promedio del historial del modelo.

## 🚀 III. Interactividad en la Explicación
1. **Análisis What-if:** Permitir que el usuario cambie un valor en la UI (ej: aumentar el precio) y ver en tiempo real cómo cambia la explicación del modelo.

---

> **Check de Certificación de XAI:**
> - [ ] ¿Los gráficos de explicabilidad coinciden con los datos entregados por el backend?
> - [ ] ¿El usuario final puede entender por qué el modelo tomó una decisión específica?
> - [ ] ¿Se han traducido los términos técnicos a conceptos de negocio?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

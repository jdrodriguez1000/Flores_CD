---
name: code-refactoring-expert
description: Protocolo para la conversión de código experimental (Notebooks) a código de producción (.py) modular, limpio y testeable.
user-invocable: false
agent: ai-ml-engineer
allowed-tools: [Read, Write, Edit, Bash]
---

## 🏗️ I. Extracción de Lógica desde Notebooks
El agente debe limpiar el desorden experimental:
1. **Identificación de Funciones Core:** Extraer solo el código necesario para el entrenamiento e inferencia final.
2. **Eliminación de Artefactos Visuales:** Remover lógica de ploteo (Matplotlib/Seaborn) y prints innecesarios del flujo de producción.
3. **Parametrización:** Convertir variables hardcodeadas en argumentos de función o parámetros de configuración.

## 📐 II. Aplicación de Principios de Ingeniería (SOLID / DRY)
1. **Simplificación de Lógica:** Refactorizar bloques de código repetitivos en utilidades comunes.
2. **Robustez de Entrada:** Validar que los datos de entrada en el pipeline `.py` manejen valores nulos o tipos incorrectos de forma graciosa.
3. **Estandarización de Estilo:** Asegurar el cumplimiento de PEP8 y las reglas de linting del proyecto.

## 🚀 III. Verificación de Paridad
1. **Test de Consistencia:** Ejecutar el código refactorizado y el notebook original con los mismos datos, verificando que los resultados (pesos, modelos, predicciones) sean idénticos bit a bit.

---

> **Check de Certificación de Refactorización:**
> - [ ] ¿Se ha eliminado todo el código basura y experimental del pipeline final?
> - [ ] ¿El código es modular y fácil de mantener para otros ingenieros?
> - [ ] ¿Se ha verificado la paridad de resultados entre el notebook y el archivo .py?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

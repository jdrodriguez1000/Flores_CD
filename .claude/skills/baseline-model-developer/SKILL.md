---
name: baseline-model-developer
description: Protocolo para establecer el Modelo Control como Baseline oficial del proyecto, instanciar el Modelo Tratamiento como Challenger, y preparar la infraestructura de experimentos para el Shadow Test.
user-invocable: false
agent: ai-data-scientist
allowed-tools: [Read, Write, Edit, Python-Interpreter]
---

## 🏗️ I. Definición del Baseline a partir del Torneo
El Baseline **no se elige aquí**: es el **Modelo Control** ya seleccionado por el `algorithm-architecture-evaluator`. Esta skill lo formaliza e instrumenta:

1. **Instancia del Modelo Control:** Entrenar el Control con sus hiperparámetros por defecto (sin optimización) sobre el dataset Gold completo.
2. **Referencia Heurística:** Implementar un Dummy Classifier/Regressor para cuantificar la ganancia real de la IA sobre una predicción trivial.
3. **Métricas Base Oficiales:** Registrar las métricas del Control (media CV + std) como el piso oficial del proyecto. Ningún experimento futuro puede estar por debajo de este piso.

## 📐 II. Instancia del Modelo Tratamiento (Challenger)
Preparar el entorno del Challenger para el Shadow Test:

1. **Entrenamiento Inicial del Tratamiento:** Entrenar el Modelo Tratamiento con sus hiperparámetros por defecto. Registrar sus métricas iniciales.
2. **Delta de Precisión:** Documentar la diferencia de métrica primaria entre Tratamiento y Control: `Δ = Tratamiento - Control`. Este delta es el objetivo a sostener tras la optimización.
3. **Validación de No-Regresión del Control:** Confirmar que el Control sigue siendo más estable que el Tratamiento (CV std Control < CV std Tratamiento). Si esto no se cumple, escalar al `algorithm-architecture-evaluator`.

## 🚀 III. Preparación del Shadow Test
1. **Registro en Experiment Tracker (MLflow/W&B):**
   - Crear dos runs paralelos: `baseline_control` y `baseline_treatment`.
   - Etiquetar ambos con `role: control` y `role: treatment` respectivamente.
   - Fijar semillas (`random_state`) idénticas en ambos para garantizar comparabilidad.
2. **Infra de Predicciones Paralelas:** Preparar el script que genere predicciones simultáneas de Control y Tratamiento sobre el mismo batch de datos (base del Shadow Test).
3. **Artefactos de Registro:**
   - `artifacts/baseline_control/` — modelo serializado + métricas + configuración.
   - `artifacts/baseline_treatment/` — modelo serializado + métricas + configuración.

## 📊 IV. Informe de Benchmarking Inicial

| Modelo | Métrica Primaria (media CV) | CV Std | vs. Dummy | Rol |
|---|---|---|---|---|
| Dummy Classifier | ... | — | referencia | — |
| Control | ... | ... | +X% | Baseline Oficial |
| Tratamiento | ... | ... | +Y% | Shadow Challenger |

- **Hoja de Ruta:** Basado en el delta inicial, proponer qué áreas requieren mayor refinamiento en la fase de optimización de hiperparámetros.

---

> **Check de Certificación de Baseline:**
> - [ ] ¿El Modelo Control proviene del Torneo validado por `algorithm-architecture-evaluator`?
> - [ ] ¿Se han fijado semillas idénticas en Control y Tratamiento para garantizar comparabilidad?
> - [ ] ¿El Control supera significativamente al Dummy Classifier?
> - [ ] ¿El CV std del Control es menor que el del Tratamiento (Control más estable)?
> - [ ] ¿Ambos modelos están registrados en el Experiment Tracker con sus roles etiquetados?
> - [ ] ¿Existe el script de predicciones paralelas listo para el Shadow Test?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

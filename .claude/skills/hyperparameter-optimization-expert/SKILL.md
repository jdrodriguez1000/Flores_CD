---
name: hyperparameter-optimization-expert
description: Protocolo para la optimización de hiperparámetros diferenciada por rol — estabilidad para el Modelo Control y máxima precisión para el Modelo Tratamiento — usando técnicas Bayesianas u Optuna.
user-invocable: false
agent: ai-data-scientist
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Definición del Espacio de Búsqueda por Rol
La optimización **no es igual para ambos modelos**. El objetivo difiere según el rol asignado por el Torneo:

### Control — Objetivo: Estabilidad
- **Métrica de optimización:** Minimizar `CV std` (varianza entre folds) dentro del rango de métricas primarias que superen el umbral del BRD.
- **Espacio de búsqueda restringido:** Preferir rangos conservadores para parámetros de regularización (`min_child_weight`, `lambda`, `alpha`). Priorizar modelos que no sobreajusten.
- **Criterio de parada:** El Control óptimo es el que maximiza `(métrica_primaria / CV_std)` — el mejor ratio precisión/estabilidad.

### Tratamiento — Objetivo: Máxima Precisión
- **Métrica de optimización:** Maximizar la métrica primaria del BRD (ej: F1-macro, AUC-ROC, MAE).
- **Espacio de búsqueda amplio:** Explorar configuraciones más agresivas (`learning_rate`, `max_depth`, `num_leaves`) aceptando mayor varianza si gana en precisión.
- **Restricción de Efficiency Gate:** El espacio de búsqueda debe estar acotado para que el modelo resultante siga dentro de los límites de latencia/memoria aprobados en el Torneo.

## 📐 II. Ejecución con Estrategia de Poda (Pruning)
Ambas optimizaciones comparten la misma infraestructura técnica pero con estudios Optuna separados:

1. **Bayesian Optimization:** Uso de procesos gaussianos para explorar el espacio de forma inteligente reduciendo el número de iteraciones.
2. **Early Stopping / Pruning:** Implementar `MedianPruner` para detener experimentos que no prometen superar al mejor resultado actual del mismo run.
3. **Cross-Validation Robusta:** Mínimo 5-fold estratificado. Cada combinación de hiperparámetros se evalúa con la misma semilla fijada en el `baseline-model-developer`.
4. **Estudios Paralelos en MLflow:** Registrar `study_control` y `study_treatment` como runs separados con tag `role`.

```python
# Estructura de estudios separados
study_control   = optuna.create_study(direction="maximize", study_name="control_stability")
study_treatment = optuna.create_study(direction="maximize", study_name="treatment_precision")
```

## 🚀 III. Certificación de Hiperparámetros

1. **Análisis de Sensibilidad:** Para cada estudio, identificar qué parámetros fueron determinantes.
2. **Validación de Roles:** Confirmar que tras la optimización:
   - El Control mantiene menor `CV std` que el Tratamiento.
   - El Tratamiento mantiene mayor métrica primaria que el Control.
   - Si los roles se invierten, escalar al `algorithm-architecture-evaluator` para revisión del Torneo.
3. **Exportación de Configuraciones:** Generar dos artefactos independientes:
   - `config/hyperparams_control.yaml`
   - `config/hyperparams_treatment.yaml`

---

> **Check de Certificación de Optimización:**
> - [ ] ¿Se han ejecutado estudios Optuna **separados** para Control y Tratamiento?
> - [ ] ¿El Control se optimizó por ratio `precisión/estabilidad` y no solo por métrica primaria?
> - [ ] ¿El Tratamiento sigue dentro del Efficiency Gate (latencia/memoria) tras la optimización?
> - [ ] ¿Los roles (Control más estable, Tratamiento más preciso) se mantienen post-optimización?
> - [ ] ¿Se han registrado ambos estudios en MLflow con tags `role: control` / `role: treatment`?
> - [ ] ¿Se ha evitado el sobreajuste mediante validación cruzada adecuada en ambos?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

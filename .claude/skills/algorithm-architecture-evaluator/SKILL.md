---
name: algorithm-architecture-evaluator
description: Protocolo para la evaluación y selección de arquitecturas de Machine Learning mediante un Torneo de Algoritmos, produciendo dos finalistas con roles complementarios — Modelo Control (estable y simple) y Modelo Tratamiento (máxima precisión) — como base del Shadow Test.
user-invocable: false
agent: ai-data-scientist
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Diagnóstico de los Datos Gold
Antes de convocar candidatos al torneo, diagnosticar la naturaleza del dato:
1. **Linealidad vs. No-linealidad:** ¿Relaciones simples capturan la señal o se requiere alta expresividad?
2. **Cardinalidad y Estructura:** ¿Datos tabulares (XGBoost/LightGBM) o secuencias/texto (Transformers/RNN)?
3. **Restricciones de Negocio:** Interpretabilidad requerida, latencia de inferencia exigida (SLA), volumen de datos en producción.

## 🚫 II. Filtro de Eficiencia (Elimination Round)
**Antes de cualquier evaluación de precisión**, aplicar el filtro de descarte. Un candidato es eliminado si:
- Tiempo de entrenamiento en el dataset Gold supera el umbral definido en el SAD (ej: > 30 min en CV 5-fold).
- Latencia de inferencia por fila supera el SLA del Architect (ej: > 100 ms).
- Huella de memoria en producción supera el límite del stack de despliegue.

> **Regla Absoluta:** Un algoritmo eliminado en este filtro **no puede ser rehabilitado** aunque su precisión sea superior. El costo operativo es un criterio no negociable.

Documentar en tabla:

| Candidato | Train Time | Inference Latency | Memory | Decisión |
|---|---|---|---|---|
| XGBoost | ... | ... | ... | ✅ Pasa |
| LightGBM | ... | ... | ... | ✅ Pasa |
| Deep MLP | ... | ... | ... | ❌ Eliminado |

## 🏆 III. Torneo de Algoritmos (Round Robin)
Con los candidatos supervivientes del filtro de eficiencia, ejecutar evaluación comparativa en cross-validation (mínimo 5-fold estratificado):

1. **Métrica Primaria de Precisión:** La definida en el BRD (ej: F1-macro, AUC-ROC, MAE).
2. **Métrica de Estabilidad:** Desviación estándar de la métrica primaria entre folds (`CV std`).
3. **Complejidad del Modelo:** Número de hiperparámetros, profundidad, grado de caja negra.

Documentar resultados completos:

| Candidato | Métrica Primaria (media) | CV Std | Complejidad | Interpretabilidad |
|---|---|---|---|---|
| Logistic Reg | ... | ... | Baja | Alta |
| Random Forest | ... | ... | Media | Media |
| LightGBM | ... | ... | Media | Media |
| CatBoost | ... | ... | Media | Media |

## 🎯 IV. Selección Dual: Control y Tratamiento

### Modelo Control (Baseline Oficial)
- **Criterio de selección:** El candidato con **menor varianza en CV** (más estable) y **menor complejidad** entre los que superan el umbral mínimo de precisión del BRD.
- **Rol en Shadow Test:** Referencia de producción. Sus predicciones son las que el negocio usa hoy.
- **Documentar:** Nombre, hiperparámetros por defecto, métricas de CV, justificación de estabilidad.

### Modelo Tratamiento (Challenger)
- **Criterio de selección:** El candidato con **mayor métrica primaria** (máxima precisión) entre los supervivientes del filtro de eficiencia.
- **Rol en Shadow Test:** Desafía al Control en paralelo. Sus predicciones se evalúan sin impacto productivo hasta obtener aprobación del analista.
- **Documentar:** Nombre, hiperparámetros iniciales, delta de precisión sobre el Control, riesgo de complejidad.

> **Caso de empate:** Si Control y Tratamiento resultan ser el mismo algoritmo (ej: LightGBM gana en estabilidad Y precisión), se designa ese como Control y el segundo en el ranking de precisión como Tratamiento.

## 🚀 V. Matriz de Decisión Final
Documentar la elección final y emitir el artefacto de salida:

```yaml
tournament_result:
  control_model:
    algorithm: <nombre>
    rationale: "Mayor estabilidad (CV std = X). Supera umbral mínimo de precisión del BRD."
  treatment_model:
    algorithm: <nombre>
    rationale: "Máxima precisión (métrica = X). Dentro del Efficiency Gate."
  discarded:
    - algorithm: <nombre>
      reason: "Eliminado en Filtro de Eficiencia: train_time = X min > umbral."
```

---

> **Check de Certificación del Torneo:**
> - [ ] ¿Todos los candidatos pasaron primero por el Filtro de Eficiencia antes de ser evaluados por precisión?
> - [ ] ¿El Modelo Control se eligió por estabilidad (CV std) y no solo por precisión?
> - [ ] ¿El Modelo Tratamiento supera al Control en la métrica primaria del BRD?
> - [ ] ¿La elección es compatible con el stack de serialización definido en el SAD?
> - [ ] ¿Se ha emitido el artefacto YAML con la justificación de ambos finalistas?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

# decisions.md — Registro Historico de Decisiones y Lecciones Aprendidas

> Registro append-only. Nunca se sobrescribe. Los nuevos registros se agregan al final con separadores claros.
> Prioridad: el "por que" de cada decision es mas valioso que la descripcion tecnica del cambio.

---

## [2026-04-28] — Sesion de Cierre: Phase Discovery / Iteracion 0.0

- **Rama:** `slice/F0-backlog-init`
- **Agente de cierre:** `ai-session-steward`
- **Tareas completadas en sesion:** T0.4

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-001** | Metricas tecnicas aprobadas: F1-score macro >= 0.95, Accuracy >= 0.95, Recall por clase >= 0.90, advertencia de confianza si max(prob) < 0.60 | El usuario aprobo los thresholds recomendados. AUC-ROC multiclase fue descartado porque no aporta valor operativo adicional con dataset balanceado. La advertencia de confianza es el mecanismo clave para reducir escalaciones al comite de expertos. | Define el DoD tecnico del modelo. Todo experimento en Phase Modeling debe medirse contra estos umbrales. |
| **D-002** | Modalidad de interaccion del dashboard: ingreso manual de 4 medidas (no CSV masivo, no API REST) | El caso de uso operativo es clasificacion individual analista por analista. CSV y API fueron descartados explicitamente por el usuario por no corresponder al flujo real de trabajo. | Simplifica la arquitectura del dashboard. No se necesita parser de CSV ni endpoint REST en el alcance actual. |
| **D-003** | Flujo de baja confianza: estado "revision pendiente" con confirmacion por segundo analista (no escalacion directa al comite, no descarte) | Preserva el trabajo realizado, evita convocar al comite de 3 expertos para casos individuales de baja confianza, y reduce el costo operativo cuantificado en ~200 horas-agente/semana. | El dashboard requiere una capa de estado para predicciones ("confirmada" / "pendiente") y una vista de cola de revisiones. Implica persistencia local. |
| **D-004** | Persistencia: CSV o SQLite local (no base de datos remota, no cloud) | Compatible con el stack Streamlit local sin infraestructura adicional. El usuario aprobo explicitamente. Coherente con la restriccion de ejecucion local. | La capa de datos es ligera. No requiere migraciones de esquema complejas para el alcance actual. |
| **D-005** | Doble proposito del proyecto: resolver clasificacion de Iris Y servir como plantilla replicable para otras clasificaciones botanicas | El cliente necesita extender este tipo de trabajos a otras flores. Documentado explicitamente en shared_understanding.md (Q4). | La arquitectura debe ser modular desde el diseno inicial. El SAD (T0.10) y el SpecDD (T0.11) deben reflejar esta restriccion de extensibilidad. |

---

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| **L-001** | El ROI del proyecto no es mejora de precision del modelo: es capacidad operativa recuperada. Cuantificar el impacto en horas-agente (no en accuracy) es lo que hace al argumento de negocio convincente. | Surgio en Q3 de la entrevista. La cuantificacion (600 revisiones/semana -> ~200 horas-agente perdidas -> 360 analisis liberables) convirtio un requerimiento vago en un KPI operativo concreto. |
| **L-002** | Antes de definir metricas tecnicas, hay que entender el mecanismo de fallo del proceso humano. El threshold de confianza < 0.60 no es una metrica academica: es el puente entre la salida del modelo y la reduccion del comite de expertos. | El modelo puede tener F1 = 0.98 pero si no tiene mecanismo de advertencia de baja confianza, el flujo operativo de revision no cambia. |
| **L-003** | El shared_understanding.md debe redactarse como fuente de verdad inmutable para el BRD. En T0.6, el agente `ai-business-strategist` no debe reinventar requerimientos: debe traducir directamente las respuestas aprobadas del Historial de Interrogacion a lenguaje de requerimientos formales. | La entrevista ya tiene toda la informacion necesaria. Agregar interpretaciones no validadas en el BRD seria una violacion de Soberania Documental. |

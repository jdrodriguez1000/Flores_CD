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

---

## [2026-04-28] — Sesion de Cierre: Phase Discovery / Iteracion 0.0 — T0.5

- **Rama:** `slice/F0-backlog-init`
- **Agente de cierre:** `ai-session-steward`
- **Tareas completadas en sesion:** T0.5

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-006** | Crear `docs/references/config.md` como cedula de identidad oficial del proyecto con todos los IDs externos y metadatos de estado | Un proyecto con multiples agentes necesita una fuente de verdad unica para IDs externos (NotebookLM, GitHub) y estado operativo. Sin este archivo, cada agente debia suponer o reinventar valores como el NOTEBOOK_ID, generando riesgo de inconsistencias. | Todo agente debe leer `config.md` en apertura de sesion. Elimina la necesidad de repetir IDs en el `handoff.md` o en instrucciones ad-hoc. |

---

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| **L-004** | Los IDs de herramientas externas (NotebookLM, plataformas cloud) deben centralizarse en un unico archivo de configuracion desde la primera sesion. Pasarlos en el contexto de cada invocacion es fragil y propenso a error humano. | T0.5 fue necesaria precisamente porque el NOTEBOOK_ID se habia estado pasando manualmente en cada invocacion de agente. Centralizar en `config.md` hace el sistema autosuficiente. |

---

## [2026-04-28] — Control de Cambios CC-001 + Auditoria BRD v1.0.0

- **Rama:** `slice/F0-backlog-init`
- **Agente:** `ai-business-strategist`
- **Documento afectado:** `docs/governance/BRD.md` (v1.0.0 → v1.1.0)

---

### Control de Cambios

| CC-ID | Cambio | Justificacion | Ficha |
| :--- | :--- | :--- | :--- |
| **CC-001** | Agregar `analista_id` (texto libre) al flujo de prediccion y confirmacion | Contradiccion logica entre RF-04/RF-05 (requerian distinguir analistas) y la exclusion de autenticacion. Solucion minima sin cambio de arquitectura. | `docs/changes/CC-001.md` |

### Correcciones de Auditoria Aplicadas

| ID | Descripcion |
| :--- | :--- |
| **C-2** | CA-02 reemplazado con valores concretos ejecutables (sepal_length=6.3, sepal_width=2.5, petal_length=4.9, petal_width=1.5) |
| **C-3** | RF-05 define tabla de transiciones de estado: `pendiente` → `confirmada` / `corregida`. Cierra el flujo. |
| **I-1** | CA-06 agregado para RF-07: criterio verificable de extensibilidad modular |
| **I-2** | RF-06 incluye esquema minimo de persistencia con 14 campos definidos |
| **I-3** | KPI-N-01 incluye baseline proxy (600/semana) y mecanismo de medicion (4 semanas post-produccion) |
| **I-4** | RF-01b incluye rangos validos por campo y comportamiento bloqueante ante input invalido |

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| **L-005** | Una auditoria devil's advocate del BRD antes de avanzar a T0.7 (BDD) previene que los escenarios Gherkin hereden vacios logicos. El costo de corregir en el BRD es minimo; el costo de corregir en behavior.md + tests es mayor. | La contradiccion CC-001 hubiera generado un RF inimplementable que solo se habria detectado al escribir el escenario Gherkin de US-02/US-03. |
| **L-006** | Los criterios de aceptacion (CA) deben tener valores concretos desde el BRD. Un CA sin inputs numericos especificos no es un test — es una intencion. | CA-02 original decia "valores ambiguos en zona de frontera" sin especificarlos, haciendo el UAT no determinista. |

---

## [2026-04-28] — Auditoria Devil's Advocate BRD Rondas 1 y 2 (v1.1.0 → v1.3.0)

- **Rama:** `slice/F0-backlog-init`
- **Agente de cierre:** `ai-session-steward`
- **Tareas completadas en sesion:** Auditoria del BRD en 2 rondas iterativas → BRD v1.3.0 Aprobado

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-006** | Despliegue: una unica instancia Streamlit en PC compartida con acceso secuencial de 5 analistas | El cliente no tiene infraestructura de servidores. La concurrencia real es baja (5 analistas, acceso secuencial). Streamlit local es la solucion minima que evita complejidad de redes o autenticacion. | El SAD (T0.10) no debe disenar para concurrencia alta. La arquitectura es single-instance por decision de negocio. |
| **D-007** | Persistencia: SQLite como unico mecanismo de escritura; CSV solo para exportacion de lectura | SQLite resuelve el riesgo de corrupcion de datos que tiene CSV cuando multiples procesos escriben al mismo archivo. CSV queda habilitado unicamente como formato de exportacion (lectura). Actualiza parcialmente D-004. | El Contrato de Datos (T0.12) debe especificar SQLite como backend de escritura. El RF-06 del BRD ya asume este esquema. |
| **D-008** | Control de auto-confirmacion: organizacional, no tecnico. El sistema NO bloquea que un analista confirme su propia prediccion | Implementar un bloqueo tecnico requeriria autenticacion real, lo cual fue descartado (CC-001). El costo de complejidad supera el riesgo de uso indebido dado el contexto de equipo pequeno. El control es via proceso operativo del cliente. | behavior.md (T0.7) no debe incluir escenario de bloqueo tecnico de auto-confirmacion. El flujo de US-03 es permisivo a nivel sistema. |
| **D-009** | Extensibilidad v1.0 restringida a datasets con mismo numero de features (4 numericas), distinto target. Formulario dinamico para N-features requiere CC aprobado | Mantiene el formulario de ingreso simple y testeable en v1.0. Evitar la sobre-ingenieria de un formulario dinamico sin un caso de uso concreto y aprobado. | CA-06 del BRD refleja esta restriccion. El SAD y SpecDD deben documentar este limite explicito de extensibilidad. |
| **D-010** | CA-02 dividida en CA-02a y CA-02b para desbloquear el flujo BDD sin esperar al modelo entrenado | CA-02 original mezclaba la verificacion del mecanismo de advertencia (testeable con mock) con la validacion de inputs especificos post-entrenamiento. Dividirla permite iniciar behavior.md y los tests inmediatamente. | CA-02a habilita T0.7 (behavior.md) sin bloqueo. CA-02b se completa en el anexo de calibracion post Phase Modeling. Los tests de CA-02a usan mock de modelo con prob=[0.45, 0.30, 0.25]. |
| **D-011** | Umbral de baja confianza 0.60: calibrable post-entrenamiento. Criterio objetivo: ≤15% de predicciones del test set de Iris deben activar la advertencia | Un umbral fijo sin criterio de calibracion es un parametro magico. El criterio del 15% hace verificable si el umbral es correcto para la distribucion real de Iris, sin imponer un valor arbitrario permanente. | El valor 0.60 es el punto de partida para Phase Modeling. Si la calibracion arroja que ≤15% del test set activa la advertencia con ese umbral, se mantiene. Si no, se ajusta con CC. |

---

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| **L-007** | Una segunda ronda de auditoria devil's advocate es necesaria cuando la primera ronda genera cambios estructurales. Los cambios de Ronda 1 pueden introducir nuevas inconsistencias que solo se detectan al releer el BRD completo con ojos frescos. | CA-02 fue "reparada" en Ronda 1 con valores concretos, pero el arreglo creo un nuevo problema: mezclaba un test de mecanismo (mock) con un test de datos reales (post-entrenamiento). Solo Ronda 2 lo detecto. |
| **L-008** | Dividir un criterio de aceptacion en dos (CA-02a / CA-02b) es una tecnica valida para desbloquear el ciclo BDD sin sacrificar la trazabilidad. No hay que esperar a tener todos los insumos para iniciar el behavior.md; se puede avanzar en lo testeable y marcar el resto como pendiente con placeholder explicito. | Sin esta division, T0.7 hubiera quedado bloqueada hasta terminar Phase Modeling. Con la division, CA-02a habilita el 90% del behavior.md inmediatamente. |
| **L-009** | Los parametros calibrables (como thresholds de confianza) deben tener un criterio de aceptacion objetivo desde el BRD, no solo un valor inicial. La pregunta correcta no es "cual es el umbral" sino "como sabemos que el umbral es correcto". | El umbral 0.60 sin criterio de calibracion es un parametro magico. El criterio ≤15% lo convierte en una hipotesis verificable. |

---

## [2026-04-28] — CC-002: Shadow Testing

- **Rama:** `slice/F0-backlog-init`
- **Agente:** `ai-business-strategist`
- **Documento afectado:** `docs/governance/BRD.md` (v1.3.0 → v1.4.0)

---

### Control de Cambios

| CC-ID | Cambio | Justificacion | Ficha |
| :--- | :--- | :--- | :--- |
| **CC-002** | Incorporar shadow testing con modelo control y modelo tratamiento | El cliente exige evaluar modelos candidatos en produccion real sin impacto operativo para los analistas. Ejecucion simultanea de ambos modelos; solo el control es visible. | `docs/changes/CC-002.md` |

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-012** | Shadow testing como capacidad de primera clase en v1.0: modelo control (visible) y modelo tratamiento (sombra) con almacenamiento diferenciado | El cliente lo exige para evaluar nuevas versiones sin interrumpir el flujo operativo. La arquitectura de doble modelo desde v1.0 evita una refactorizacion costosa en fases posteriores. | El SAD (T0.10) debe disenar la capa de gestion de modelos con dos artefactos y roles. El SpecDD (T0.11) debe definir el dispatcher de prediccion. El Contrato de Datos (T0.12) debe incluir los 3 nuevos campos del esquema SQLite. behavior.md (T0.7) debe incluir escenarios Gherkin de shadow testing. |
| **D-013** | El modelo tratamiento NO participa en el flujo de estados `pendiente/confirmada/corregida`. Sus registros tienen estado fijo `shadow`. | Mantener el flujo operativo simple para el analista. La comparacion de modelos es una actividad offline del Product Owner, no una tarea del analista. | behavior.md (T0.7) no debe incluir escenarios de revision de predicciones shadow por parte del analista. |
| **D-014** | La promocion del modelo tratamiento a control es manual y requiere CC aprobado. No hay promocion automatica. | Garantiza que toda transicion de modelo en produccion pase por gobernanza explicita. Evita regresiones silenciosas. | El SAD (T0.10) debe documentar el mecanismo de swap de modelos como operacion manual con trazabilidad. |

---

## [2026-04-28] — CC-003: Decision Explicita del Analista 1

- **Rama:** `slice/F0-backlog-init`
- **Agente:** `ai-business-strategist`
- **Documento afectado:** `docs/governance/BRD.md` (v1.4.0 → v1.5.0)

---

### Control de Cambios

| CC-ID | Cambio | Justificacion | Ficha |
| :--- | :--- | :--- | :--- |
| **CC-003** | Agregar decision explicita del Analista 1 (Aceptar/Rechazar) tras cada clasificacion | Sin este mecanismo el escenario de FP de alta confianza quedaba sin cobertura. Ademas provee el ground truth necesario para que el shadow test sea comparable. | `docs/changes/CC-003.md` |

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-015** | El Analista 1 debe emitir siempre una decision explicita (Aceptar/Rechazar) tras cada clasificacion. Alta confianza + Aceptar = `confirmada_a1` (caso cerrado). Cualquier Rechazo o Baja confianza = `pendiente` (va a revision). | Cierra el gap del Falso Positivo de alta confianza y genera ground truth operativo para el shadow test sin infraestructura adicional. | RF-04 y RF-05 del BRD reflejan la maquina de estados completa. El SpecDD (T0.11) debe definir la interfaz del dispatcher con el parametro de decision del Analista 1. behavior.md (T0.7) debe incluir escenarios Gherkin para los cuatro caminos: alta confianza + acepta, alta confianza + rechaza, baja confianza + acepta, baja confianza + rechaza. |

---

## [2026-04-28] — Sesion de Cierre: Phase Discovery / Iteracion 0.0 — Gobernanza de Agentes: Torneo de Algoritmos y Shadow Testing

- **Rama:** `slice/F0-backlog-init`
- **Agente de cierre:** `ai-session-steward`
- **Archivos modificados:** 6 archivos del escuadron (2 agentes, 4 skills)

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-016** | El Modelo Control se selecciona por minimo CV std (estabilidad) y el Modelo Tratamiento por maxima precision, con el Efficiency Gate como filtro previo no negociable para todos los candidatos | La estabilidad del Control garantiza que el flujo operativo del analista no se vea afectado por varianza de prediccion. El Tratamiento puede optimizar precision pura porque opera en sombra sin impacto visible. El Efficiency Gate es no negociable porque el hardware del cliente (PC compartida, Streamlit local) impone restricciones reales de recursos. | El `algorithm-architecture-evaluator` aplica el Efficiency Gate antes de evaluar precision. El `hyperparameter-optimization-expert` tiene dos estudios Optuna separados con objetivos distintos por rol. El artefacto YAML de salida del torneo diferencia `control_model` y `treatment_model`. |
| **D-017** | El `ai-business-strategist` es el punto de captura de la intencion de Shadow Testing en la Fase 0 (Hard Rule #6), no el `ai-data-scientist` | La decision de hacer Shadow Testing es una decision de negocio (quien aprueba el paso a produccion, cual es el criterio de aceptacion del tratamiento). Si se captura en Fase de Descubrimiento, puede reflejarse en el BRD y en el behavior.md desde el inicio, evitando un CC costoso en fases posteriores. | La Hard Rule #6 del `ai-business-strategist` formaliza las preguntas obligatorias durante el ritual ask-me. El BRD puede incorporar una seccion de Estrategia de Despliegue con Shadow Testing si el cliente lo confirma en Flores_CD. |
| **D-018** | El `feature-importance-analyzer` genera una tabla comparativa de rankings SHAP y Permutation Importance para ambos modelos, y emite una alerta para el Shadow Test cuando los patrones SHAP divergen significativamente | Una divergencia en importancia de features entre Control y Tratamiento indica que los modelos han aprendido señales distintas. Esto es informacion critica para el Product Owner antes de decidir la promocion del Tratamiento a Control. Sin esta alerta, el swap podria hacerse basandose solo en accuracy agregado, ocultando diferencias de comportamiento interno. | El `feature-importance-analyzer` produce un artefacto adicional de alerta de divergencia. El SAD (T0.10) debe incluir este artefacto como salida del pipeline de evaluacion. |

---

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| **L-010** | El paradigma de Torneo de Algoritmos requiere que el Efficiency Gate preceda a cualquier evaluacion de precision. Evaluar precision de un modelo que no puede operar en el hardware objetivo es trabajo desperdiciado. La arquitectura de agentes debe reflejar este orden de filtros explicitamente. | Al disenar el `algorithm-architecture-evaluator`, el orden natural era evaluar precision primero y luego verificar recursos. Invertir este orden (Efficiency Gate primero) es contraintuitivo pero correcto: evita optimizar hiperparametros de candidatos que nunca llegaran a produccion. |
| **L-011** | La captura temprana de la intencion de Shadow Testing en la Fase 0 es mas barata que un CC en la Fase de Modelado. Un requisito de Shadow Testing descubierto cuando ya existe un unico modelo entrenado obliga a redisenar la arquitectura de gestion de modelos y el esquema de persistencia. | La sesion anterior (CC-002) incorporo Shadow Testing al BRD despues de que ya estaba redactado. Aunque el CC fue exitoso, el costo hubiera sido mayor si se detectaba en la Fase de Ingesta o Modelado. La Hard Rule #6 previene que esto se repita en proyectos futuros. |
| **L-012** | Los skills de un agente deben diferenciarse por el rol del artefacto que producen, no solo por la tecnica que aplican. SHAP se aplica igual a Control y a Tratamiento, pero el artefacto de salida (tabla comparativa + alerta de divergencia) tiene valor distinto para el Product Owner que SHAP de un modelo aislado. El diseño de skills debe explicitar este contexto de uso. | Al actualizar el `feature-importance-analyzer`, la primera version del skill simplemente doblaba el analisis SHAP. La version final agrega la tabla comparativa y la señal de alerta, que son los entregables con valor real para la decision de promocion de modelo. |

---

## [2026-04-29] — Auditoria Devil's Advocate BRD v1.5.0 → v1.6.0 (7 micro-decisiones)

- **Rama:** `slice/F0-backlog-init`
- **Agente:** `ai-business-strategist`
- **Documento afectado:** `docs/governance/BRD.md` (v1.5.0 → v1.6.0)

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-019** | Al rechazar la prediccion, el selector de especie excluye la especie predicha por el modelo control (G-001) | El rechazo debe implicar una alternativa real. Permitir rechazar eligiendo la misma especie generaria un registro semanticamente contradictorio (`decision_analista1=rechazada`, `especie_analista1=especie_predicha`). Si el A1 quiere revision sin cambiar la especie, el mecanismo correcto es la advertencia de baja confianza. | RF-04 actualizado. El SpecDD debe reflejar que el selector de especie en el flujo de rechazo filtra la especie predicha del modelo control. |
| **D-020** | "Corregir" del Analista 2 permite elegir cualquier especie, incluida la del modelo control (G-002) | El A2 discrepa del criterio del A1, no del modelo. Si el modelo tenia razon y el A1 se equivoco al rechazar, el A2 debe poder restaurar la especie del modelo. Restringir la eleccion del A2 a "distinta del modelo y del A1" bloquearia este caso valido. | RF-05 actualizado. El SpecDD debe reflejar que el selector del A2 en "Corregir" muestra las 3 especies sin filtro. |
| **D-021** | La vista enriquecida del A2 muestra el origen del estado pendiente: `baja_confianza_automatica` vs `rechazo_analista1` (G-003) | Sin este indicador, el A2 no puede distinguir si el caso esta en revision porque el modelo es incierto o porque el A1 discrepo. Son dos situaciones con interpretacion distinta. El campo `baja_confianza` (bool) ya existe en SQLite; solo requiere ser expuesto en la UI. | RF-05 actualizado. El SpecDD y el SAD deben incluir la logica de derivacion del indicador: `baja_confianza=True` → `baja_confianza_automatica`; `baja_confianza=False` y `decision_analista1=rechazada` → `rechazo_analista1`. |
| **D-022** | KPI-T-05 ground truth simplificado a `especie_confirmada` para todos los estados terminales (A-001/I-002) | La logica condicional anterior (`confirmada_a1` → especie predicha; `confirmada` → `especie_analista1`; `corregida` → `especie_confirmada`) generaba NULL para registros `confirmada` donde el A1 acepto. `especie_confirmada` es siempre deterministica en estados terminales y elimina la logica condicional del calculo del KPI. | KPI-T-05 actualizado. El SpecDD del modulo de evaluacion offline debe leer `especie_confirmada` directamente, sin condicionales por estado. |
| **D-023** | Dominio completo del campo `estado` en SQLite: `pendiente / confirmada_a1 / confirmada / corregida / shadow` (A-003/I-001) | El campo en RF-06 solo listaba 3 valores; los estados `confirmada_a1` (CC-003) y `shadow` (CC-002) habian sido introducidos en el BRD pero no reflejados en la definicion del campo de persistencia. La inconsistencia hubiera generado un esquema SQLite defectuoso. | RF-06 actualizado. El Contrato de Datos (T0.12) debe usar este dominio completo como restriccion CHECK en la columna. |
| **D-024** | Campo `prediction_batch_id` (UUID) agregado al esquema SQLite como 21er campo; compartido entre registro control y shadow del mismo ciclo de prediccion (CB-004) | Sin este campo, el join para KPI-T-05 dependia de `(timestamp, analista_id)` con tolerancia de segundos, lo cual es fragil. Un UUID generado en el dispatcher al invocar ambos modelos garantiza un join exacto y sin ambiguedad. | RF-06 actualizado (21 campos). RF-08 actualizado (el dispatcher genera el UUID). El Contrato de Datos (T0.12) debe incluir este campo. El SpecDD debe definir que el dispatcher de predicciones genera el `prediction_batch_id` antes de invocar ambos modelos. |
| **D-025** | RF-08 distingue dos estados del modelo tratamiento: "no configurado" (operacion silenciosa) vs "fallo en runtime" (log interno, continua solo con control, no escribe registro shadow) (A-004) | La distincion importa para el implementador del dispatcher: "no configurado" es un estado esperado de operacion (antes del primer despliegue del tratamiento), mientras que "fallo en runtime" es un error que debe ser observable internamente sin impactar al analista. Tratarlos igual ocultaria errores operativos reales. | RF-08 actualizado. El SpecDD debe definir la interfaz del dispatcher con los dos modos de degradacion. El SAD debe incluir la estrategia de logging para fallos de runtime del modelo tratamiento. |

---

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| **L-013** | Una auditoria devil's advocate del BRD inmediatamente antes de redactar behavior.md es el momento de maxima eficiencia: los hallazgos se resuelven como micro-decisiones (sin CC formal) porque aun no hay escenarios Gherkin ni tests escritos que deban actualizarse en cascada. El costo de resolver un vacio en el BRD crece exponencialmente con cada capa de artefacto que lo hereda. | Los 7 hallazgos de prioridad Alta de esta auditoria hubieran generado escenarios Gherkin ambiguos, un esquema SQLite defectuoso y un KPI-T-05 con ground truth NULL. Detectarlos antes de T0.7 redujo el costo de correccion a ediciones puntuales en un solo documento. |
| **L-014** | El campo `prediction_batch_id` es un ejemplo de infraestructura de datos que solo se identifica al razonar sobre el calculo de un KPI concreto. Si el KPI-T-05 no hubiera sido definido con precision desde el BRD, la necesidad del UUID de correlacion no habria emergido hasta la fase de implementacion del modulo de evaluacion offline, cuando el esquema SQLite ya estaria fijo. | CB-004 fue identificado por el agente al cruzar la definicion de KPI-T-05 con la logica de escritura de registros shadow. El BRD debe especificar KPIs con suficiente precision para que los campos de soporte necesarios emerjan en la fase de gobernanza, no en la de implementacion. |

---

## [2026-04-29] — Auditoria Devil's Advocate BRD v1.6.0 → v1.7.0 (5 micro-decisiones)

- **Rama:** `slice/F0-backlog-init`
- **Agente:** `ai-business-strategist`
- **Documento afectado:** `docs/governance/BRD.md` (v1.6.0 → v1.7.0)

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-026** | Cola de pendientes con filtrado tecnico: la consulta SQLite aplica `WHERE estado = 'pendiente' AND analista_id != [analista_id_sesion]`. El analista no ve sus propios casos. | Aunque D-008 establece que la auto-confirmacion no se bloquea tecnicamente, ocultar los casos propios en la cola reduce la friccion de confusion ("por que aparece mi caso aqui") sin requerir autenticacion. Es consistente con D-008 porque el bloqueo de escritura sigue siendo organizacional; solo se filtra la vista de lectura. | RF-05 actualizado. El SpecDD debe definir la consulta de la cola como parametrizada con `analista_id_sesion`. El SpecDD de la capa de persistencia debe reflejar este patron de consulta. |
| **D-027** | El label del boton "Confirmar" del A2 se renderiza de forma diferenciada segun el origen de la especie: "Confirmar especie del Analista 1: [X]" cuando `especie_analista1` tiene valor; "Confirmar especie del modelo: [X]" cuando `especie_analista1` es NULL. | Sin esta diferenciacion, el A2 no sabe si esta confirmando una decision humana o una prediccion del modelo. Son dos actos con distinto peso semantico. Hacerlo explicito en el label elimina ambiguedad sin agregar complejidad de implementacion. | RF-05 actualizado. El SpecDD de la capa UI debe definir la logica de renderizado del label como una funcion determinista sobre `especie_analista1`. El behavior.md (T0.7) debe incluir un escenario Gherkin para cada caso del label. |
| **D-028** | Tras la decision del A2 (Confirmar o Corregir), el sistema muestra un mensaje de confirmacion ("Caso cerrado correctamente") y el A2 permanece en la vista de cola con los casos restantes. El caso cerrado solo es accesible desde la vista de historial. | Un regreso silencioso sin mensaje deja al A2 sin feedback de que su accion fue registrada. El mensaje de confirmacion es el minimo necesario para cerrar el ciclo de interaccion. Permanecer en la cola (en lugar de redirigir a otra vista) es la opcion de menor friccion para analistas que procesan multiples casos en una sesion. | RF-05 actualizado. El SpecDD y el behavior.md deben incluir el estado post-accion del A2 como parte del escenario de US-03. |
| **D-029** | `prediction_batch_id` se genera y almacena en el registro del modelo control en todos los ciclos de prediccion, independientemente de si el modelo tratamiento esta configurado. Cuando no hay registro shadow, el UUID queda sin par — esto es esperado y no es un error. | Generar el UUID siempre simplifica el dispatcher (no necesita condicionales sobre la disponibilidad del tratamiento para decidir si genera el UUID). Ademas, permite que si el tratamiento se configura posteriormente, los registros de control anteriores ya tengan UUID y sean elegibles para futuros calculos de KPI-T-05 si el ground truth esta disponible. | RF-06 actualizado (`prediction_batch_id`). RF-08 actualizado (el dispatcher genera el UUID antes de cualquier invocacion a los modelos). El SpecDD debe reflejar este orden de operaciones en el dispatcher. |
| **D-030** | CA-03 y CA-04 actualizados con inputs/outputs concretos: CA-03 usa el input de CA-02 (sepal=6.3/2.5, petal=4.9/1.5) con analistas `analista_gen` y `analista_rev`; CA-04 define la verificacion como conteo exacto de N registros antes y despues del reinicio. | Los CAs sin valores concretos no son tests — son intenciones. CA-03 y CA-04 eran los unicos CAs sin inputs/outputs deterministicos del BRD. Reusar el input de CA-02 para CA-03 es eficiente porque ese input ya genera `pendiente` por construccion (max(prob) < 0.60 con el mock). | Seccion 8.3 del BRD actualizada. El behavior.md (T0.7) puede ahora escribir escenarios Gherkin deterministicos para CA-03 y CA-04 sin inventar valores. |

---

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| **L-015** | Una segunda auditoria del mismo BRD, ejecutada con ojos de implementador de behavior.md, detecta vacios de UX y flujo de navegacion que la primera auditoria (orientada a consistencia logica) no ve. Los dos tipos de auditoria son complementarios, no redundantes. | La primera auditoria (v1.5.0 → v1.6.0) se enfoco en inconsistencias de datos y logica de negocio. La segunda (v1.6.0 → v1.7.0) encontro vacios de renderizado de UI, flujo post-accion y comportamiento de campos en casos limite — vacios que solo se detectan al intentar escribir un escenario Gherkin concreto. |
| **L-016** | El filtrado tecnico de la cola de pendientes (D-026) es un caso donde la interfaz de la UI (que oculta casos propios) diverge del modelo de datos (que no tiene restriccion de escritura). Documentar esta divergencia explicitamente en el BRD previene que el implementador asuma que el filtro de lectura implica un bloqueo de escritura, o viceversa. | Sin D-026, un implementador podria razonablemente implementar el filtro de lectura Y agregar validacion de escritura (sobre-ingenieria), o no implementar el filtro de lectura porque D-008 dice que no hay bloqueo tecnico (sub-implementacion). La decision explicita elimina ambas interpretaciones erroneas. |

---

## [2026-04-29] — Auditoria Devil's Advocate BRD v1.7.0 → v1.8.0 (3 micro-decisiones)

- **Rama:** `slice/F0-backlog-init`
- **Agente:** `ai-business-strategist`
- **Documento afectado:** `docs/governance/BRD.md` (v1.7.0 → v1.8.0)

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-031** | Mock canonico de CA-02a: `prob=[setosa=0.45, versicolor=0.30, virginica=0.25]` → especie predicha `setosa` (max(prob)=0.45). El orden de clases sigue el orden alfabetico de scikit-learn: setosa=0, versicolor=1, virginica=2. `especie_analista1 = NULL` cuando el A1 acepta (alta o baja confianza); `especie_analista1 = especie elegida` cuando el A1 rechaza. | Sin el mapping explicito, el paso `Entonces la especie predicha es "[X]"` del escenario Gherkin de CA-02a no es determinista: dos implementadores pueden asumir ordenes distintos de clases y producir tests divergentes sin error de logica. El orden alfabetico de scikit-learn es la convencion canonica del stack (RNF-01). | CA-02a en seccion 8.3 actualizada con mock canonico. RF-04 actualizado con tabla de 4 caminos del A1 y valor de `especie_analista1` por camino. behavior.md (T0.7) puede escribir escenarios Gherkin deterministas para los 4 caminos del A1. |
| **D-032** | CA-03 fijada: el A1 acepta la prediccion de baja confianza (`decision_analista1=aceptada`, `especie_analista1=NULL`). El origen del pendiente en la cola es `baja_confianza_automatica`. Los campos visibles para `analista_rev` incluyen: especie predicha (`setosa`), probabilidades, origen (`baja_confianza_automatica`), decision del A1 (`aceptada`), `especie_analista1` (NULL), `analista_id` del generador (`analista_gen`). | CA-03 no declaraba la decision del A1, por lo que el campo `origen` tenia dos valores posibles (`baja_confianza_automatica` o `baja_confianza_automatica + rechazo_analista1`) dependiendo de una precondicion no fijada. Fijar la decision del A1 como `aceptada` produce el escenario mas simple, cubre el trigger automatico puro, y permite que el escenario `baja_confianza + rechazo` se documente como CA-03b en behavior.md si se requiere cobertura adicional. | CA-03 en seccion 8.3 actualizada con todos los valores concretos. El behavior.md (T0.7) puede escribir un escenario Gherkin completamente determinista para CA-03. |

---

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| L-017 | Una tercera ronda de auditoria del BRD, ejecutada con ojo de implementador de tests Gherkin, detecta vacios de contrato de datos que las auditorias previas no ven: especificamente, la ausencia de mapping entre vectores de probabilidad y nombres de clase, y la ausencia de valores NULL vs. vacio en columnas opcionales. Estos vacios son invisibles desde la perspectiva de negocio pero bloquean la escritura de steps `Entonces` deterministas. | Los hallazgos A3-H01 y A3-H02 son de naturaleza tecnica (contrato de columna SQLite, orden canonico de clases), no de negocio. Solo emergen cuando se intenta traducir el BRD a un escenario Gherkin con valores concretos en todos los pasos. La auditoria con perspectiva de implementador de tests es complementaria a la auditoria de consistencia logica y a la auditoria de UX/flujo. |

---

## [2026-04-29] — Creacion del Contrato BDD (behavior.md) — T0.7

- **Rama:** `slice/F0-backlog-init`
- **Agente:** `ai-business-strategist`
- **Documento afectado:** `docs/governance/behavior.md`

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-033** | Definicion de 8 escenarios Gherkin canonicos que cubren el 100% de los requerimientos funcionales criticos (US-01, US-02, US-03, RF-08, RF-01a) | Garantiza que el desarrollo en Phase Engineering tenga una guia de aceptacion deterministica. Los escenarios cubren los caminos de exito, de error y de flujo operativo (revisores). | El `ai-data-qa-engineer` debe implementar tests automatizados basados exactamente en estos escenarios. Define el DoD funcional del sistema. |
| **D-034** | Inclusion del escenario `RF-01a — Aislamiento de Session State` como prueba de comportamiento bloqueante | El despliegue en PC compartida hace que este requerimiento no funcional sea critico para la seguridad operativa. Incluirlo en el BDD contract asegura que se testee explicitamente la limpieza del `analista_id`. | El desarrollador del dashboard Streamlit debe asegurar que el estado no persiste entre recargas. |
| **D-035** | Uso de tablas Gherkin para mapear campos de SQLite directamente en los steps `Entonces` | Facilita la trazabilidad entre el comportamiento observado en la UI y la persistencia en la capa de datos (SQLite). | Los tests de aceptacion deben verificar no solo la UI sino tambien el estado final de la base de datos. |

---

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| **L-018** | La redaccion de escenarios Gherkin es el "compilador" de la logica de negocio. Si un requerimiento del BRD es dificil de traducir a Given/When/Then, es porque la definicion de estados o transiciones aun es ambigua. | Al redactar el escenario de US-03 (Revision), la necesidad de distinguir los dos origenes del pendiente (`baja_confianza_automatica` vs `rechazo_analista1`) se volvio evidente para que el Analista 2 sepa que esta confirmando. |
| **L-019** | El uso de "Antecedentes" (Background) en Gherkin para definir la presencia de ambos modelos (Control/Tratamiento) refuerza el paradigma de Shadow Testing en cada test, evitando que se olvide el registro shadow en los escenarios de exito del analista. | Sin el Background, los escenarios individuales podrian ignorar el registro shadow, resultando en tests que pasan pero que no verifican la integridad del batch de prediccion completo. |


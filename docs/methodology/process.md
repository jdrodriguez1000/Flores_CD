# Metodología de Gobernanza e Ingeniería de IA (SpecDD & TDD)

## Política Global de Almacenamiento de Artefactos

Para garantizar que el proyecto sea auditable, reproducible y escalable, se establece una estructura de archivos rígida para todos los agentes. Los entregables que **no representen código ejecutable** (BRDs, reportes de calidad, diagramas de arquitectura, manuales) deben almacenarse obligatoriamente en la ruta `docs/` segmentada por fases:

*   📂 **Gobernanza Base:** `docs/governance/` (BRD, SAD, SpecDD, Contrato de Datos, Backlog).
*   📂 **Phase Discovery (Discovery):** `docs/Phase_discovery/` (Reportes de factibilidad, análisis de gaps).
*   📂 **Phase Engineering (Engineering):** `docs/Phase_engineering/` (Reportes de EDA, calidad técnica).
*   📂 **Phase Modeling (Modeling):** `docs/Phase_modeling/` (Reportes de entrenamiento y validación).
*   📂 **Phase Delivery (Delivery):** `docs/Phase_delivery/` (Reportes E2E, carga y seguridad).

**📌 Regla de Oro de Organización:**
*   `src/`: Código fuente productivo (.py).
*   `tests/`: Suite de pruebas (unitarias, integración, E2E).
*   `models/`: Artefactos de modelos serializados (ONNX, Pickle).
*   `data/`: Almacenamiento de datos (Bronze, Silver, Gold).
*   `notebooks/`: Experimentación y R&D no productivo.
*   `docs/`: Documentación técnica y de negocio oficial.
*   `mockup/`: Prototipo visual interactivo (HTML/CSS). Generado por `ai-ux-designer` en la Phase Discovery. Reside en la raíz del proyecto para acceso directo del Stakeholder.

## Política de Aislamiento de Contexto (Context Management)

Para prevenir "alucinaciones sistémicas" y la sobrecarga cognitiva (*Review Overload*) de los agentes LLM, **está estrictamente prohibido dotar a un agente operativo con el contexto de todo el repositorio**. 
Se debe aplicar una regla de "necesidad de saber" (Need-to-Know basis):

*   **Aislamiento Estricto:** Cuando un agente de ingeniería recibe una tarea, el orquestador o usuario SOLO debe inyectarle en su prompt su archivo objetivo (ej. `modulo.py`), la prueba correspondiente (`test_modulo.py`) y el fragmento exacto del `SpecDD` y `behavior.md` que rigen esa tarea. Ningún otro archivo.
*   **Beneficio:** Mantiene el enfoque del agente estrictamente limitado a hacer que su código alcance el estado `GREEN`, evitando que altere código fuera de su jurisdicción (cero daños colaterales) y reduciendo el consumo innecesario de tokens.

---

# Phase Discovery: Discovery & Business Understanding (Detalle Integral)

Esta fase constituye el cimiento estratégico y técnico del proyecto. Su objetivo es transformar una necesidad de negocio ambigua en una especificación técnica ejecutable, garantizando que el desarrollo posterior sea viable, medible y alineado con el retorno de inversión ($ROI$).

---

## 1. Objetivos Críticos de la Fase
* **Alineación de Expectativas:** Definir qué se considera un "éxito" para el cliente.
* **Evaluación de Viabilidad:** Determinar si los datos actuales permiten resolver el problema propuesto.
* **Diseño de la Arquitectura Lógica:** Establecer los contratos de comunicación entre módulos antes de escribir código.

---

## 2. Equipo de Agentes (Roles de Industria)

### A. AI Business Analyst / Product Owner (El Estratega)
**Nombre:** ai-business-strategist
**Misión:** Actuar como el puente entre el lenguaje de negocio y el lenguaje técnico, **iniciando siempre con un Protocolo de Interrogación Socrática para eliminar cualquier ambigüedad.**
* **Funciones Detalladas:**
    * **Fase 0 - Protocolo de Interrogación (Habilidad `ask-me`):** Antes de redactar el BRD, el agente *NUNCA* debe asumir requerimientos. Debe ejecutar la habilidad `ask-me`: entrevistar implacablemente al usuario sobre el plan/diseño, resolviendo cada rama del árbol de decisiones paso a paso, hasta alcanzar un "Shared Understanding" (Entendimiento Compartido). Cada pregunta y respuesta debe registrarse en `docs/Phase_discovery/shared_understanding.md`.
    * **Definición de KPIs:** Traducir los objetivos validados en la Fase 0 en métricas técnicas como $Recall$ o $F_1\text{-score}$ vinculadas a un impacto financiero.
    * **Mapeo de User Stories:** Describir cómo el usuario final interactuará con la aplicación web (ej: "Como [Rol de Usuario], quiero ver la predicción de [Variable X] para tomar la decisión [Y]").
    * **Análisis de Costo-Beneficio:** Estimar si el esfuerzo de desarrollo compensa la ganancia esperada.
    * **Gherkin Authoring (BDD):** Tras la aprobación del BRD, traducir cada User Story en escenarios Given/When/Then con datos reales del dominio. Este paso es obligatorio y produce el **Contrato de Comportamiento** (`docs/governance/behavior.md`), que se posiciona entre el BRD y el SpecDD en la jerarquía de especificación: `BRD (Intención) → BDD (Comportamiento) → TDD (Corrección)`.
* **Skills:** `ask-me`, `gherkin-scenario-author`
* **Trigger:** Aprobación del BRD (`docs/governance/BRD.md`).
* **Entregable:** `docs/governance/behavior.md` (Contrato de Comportamiento vinculante para `ai-data-qa-engineer` y `ai-full-stack-sdet`).

### B. Project Configuration Manager (El Guardián de la Identidad)
**Nombre:** config-manager
**Misión:** Orquestar la inicialización y el mantenimiento de la identidad del proyecto, actuando como el puente entre la constitución (metodología) y la instancia del proyecto.
* **Funciones Detalladas:**
    * **Interrogatorio de Identidad:** Obtiene el nombre oficial, alias, descripción, propietario y IDs de fuentes de verdad externas. Se ejecuta justo después de alcanzar el entendimiento en la Fase 0.
    * **Creación de Configuración:** Invoca la habilidad `project-config` para materializar el archivo base.
* **Skill:** `project-config`
* **Trigger:** Creación del `shared_understanding.md`.
* **Entregable:** `docs/references/config.md` (Cédula de identidad del proyecto).

### C. AI Data Analytics Consultant (El Auditor)
**Nombre:** ai-data-auditor
**Misión:** Validar la "materia prima" (los datos) para asegurar que el entrenamiento del modelo sea posible.
* **Funciones Detalladas:**
    * **Data Audit:** Inventariar las fuentes de datos (SQL, NoSQL, APIs, CSVs).
    * **Gap Analysis:** Identificar qué datos faltan para cumplir con el requerimiento del Estratega.
    * **Exploratory Data Quality (EDQ):** Realizar un análisis preliminar de la salud de los datos (porcentaje de nulos, desbalance de clases, ruido).

### D. AI Solutions Architect (El Líder Técnico)
**Nombre:** ai-solutions-architect
**Misión:** Diseñar la estructura modular del software y garantizar la integridad del sistema mediante estándares de ingeniería.
* **Funciones Detalladas:**
    * **Elaboración del SAD (Software Architecture Document):** Diseña la topología del sistema (Monolito modular vs. Microservicios). Define el uso de contenedores (Docker), orquestadores (Kubernetes) y bases de datos.
    * **Definición del SpecDD (Specification-Driven Development):** Establecer las interfaces exactas de cada archivo `.py`.
    * **Diseño del Contrato de Datos:** Crear el esquema rígido de entrada y salida para evitar errores de integración en el `main.py` o la API.

### E. AI UX Designer (El Arquitecto de Experiencia)
**Nombre:** ai-ux-designer
**Misión:** Transformar las necesidades de negocio en prototipos visuales de alta fidelidad que el cliente pueda aprobar antes de que se escriba cualquier código de backend. Su trabajo ocurre en la Phase Discovery, justo después de la aprobación del reporte de factibilidad.
* **Funciones Detalladas:**
    * **Construcción del Prototipo Visual (Mockup):** Crea archivos HTML/CSS estáticos ultra-ligeros usando la técnica "Smoke and Mirrors": todo debe parecer real, pero los datos son hardcoded. Técnicas de diseño premium (Glassmorphism, Dark Mode) para asegurar el "Visual WOW".
    * **Validación de Navegación y Flujos:** Define cómo se mueve el usuario por la aplicación, cómo se ingresan los datos y cómo se presentan las predicciones del modelo.
    * **Gestión del Ciclo UAT:** Entrega el prototipo al Stakeholder para aprobación y registra el resultado en `docs/Phase_discovery/mockup.md`. Un mockup aprobado es el contrato visual vinculante para el `ai-frontend-engineer` en la Phase Delivery.
* **Skill:** `ui-ux-prototyping`
* **Trigger:** Aprobación del reporte de factibilidad (`docs/Phase_discovery/feasibility.md`).
* **Entregable:** `mockup/index.html` (prototipo) + `docs/Phase_discovery/mockup.md` (documento de aprobación).

---

## 3. Artefactos y Entregables Técnicos

| Artefacto                                | Responsable                                  | Ruta de Almacenamiento                                           | Contenido Detallado                                                                                                                                                               |
| :--------------------------------------- | :------------------------------------------- | :--------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Shared Understanding Log (Ask-Me)**    | AI Business Analyst                          | `docs/Phase_discovery/shared_understanding.md`                   | Transcripción en vivo y acuerdos de la entrevista `ask-me` (una pregunta a la vez). Base inmutable sobre la cual se redacta el BRD.                                               |
| **Project Configuration (Config)**       | Project Configuration Manager (config-manager)| `docs/references/config.md`                                      | Cédula de identidad del proyecto. Contiene nombre, alias, stakeholders, y mapeo de IDs de fuentes de verdad externas (ej. NotebookLM).                                            |
| **Business Requirements Document (BRD)** | AI Business Analyst                          | `docs/governance/`                                               | Objetivos, KPIs, restricciones de negocio y criterios de aceptación.                                                                                                              |
| **Behavior Contract (BDD)**              | AI Business Analyst (ai-business-strategist) | `docs/governance/behavior.md`                                    | Escenarios Gherkin (Given/When/Then) por User Story con datos reales del dominio. Fuente de verdad para tests E2E del `ai-full-stack-sdet` y tests RED del `ai-data-qa-engineer`. |
| **Data Feasibility Report**              | AI Data Analytics Consultant                 | `docs/Phase_discovery/`                                          | Catálogo de variables, diagnóstico de calidad y plan de mitigación de datos faltantes.                                                                                            |
| **Visual Mockup (Prototype)**            | AI UX Designer (ai-ux-designer)              | `mockup/` (raíz del proyecto) + `docs/Phase_discovery/mockup.md` | Prototipo HTML/CSS no funcional de alta fidelidad para validación de flujos y estética. Requiere aprobación del Stakeholder (UAT) antes de avanzar a Phase Engineering.           |
| **Software Architecture Document (SAD)** | AI Solutions Architect                       | `docs/governance/`                                               | Diagramas de componentes (C4 Model), diagramas de secuencia, stack tecnológico e infraestructura.                                                                                 |
| **Interface Specification (SpecDD)**     | AI Solutions Architect                       | `docs/governance/`                                               | Definición de funciones, parámetros de entrada/salida y manejo de excepciones.                                                                                                    |
| **Contrato de Datos (Data Schema)**      | AI Solutions Architect                       | `docs/governance/`                                               | Diccionario de datos técnico: tipo de dato, rango permitido y obligatoriedad.                                                                                                     |

---

## 4. El Contrato de Datos: Anatomía del Documento
Este es el componente más crítico para la comunicación entre agentes. Debe incluir:

1.  **Schema Enforcement:** Nombre exacto de la columna y tipo (int, float, string).
2.  **Validaciones Lógicas:** Por ejemplo, si el campo es `Edad`, la regla es $0 \leq x \leq 120$.
3.  **Null Policy:** ¿Es el campo crítico? Si es `True` y falta el dato, el sistema debe disparar una excepción en el **Backend**.
4.  **Transformation Rules:** Instrucciones para el **AI Business Analyst** sobre cómo tratar el dato en crudo antes de que llegue al modelo.

---

## 5. Preparación para TDD y Desarrollo Modular
Al finalizar esta fase, el **SAD**, el **behavior.md** y el **SpecDD** permiten que la creación de módulos sea una tarea de "ensamblaje" bajo la jerarquía SpecDD + BDD + TDD:

1. El **SAD** dicta la arquitectura (ej: Arquitectura de Cebolla o Hexagonal).
2. El **SpecDD** dicta la interfaz (qué hace el código — contratos y firmas).
3. El **behavior.md** dicta el comportamiento observable (cómo reacciona el sistema ante ejemplos reales — Gherkin).
4. El **TDD** dicta la prueba (qué debe validar el código para que cumpla los dos anteriores).

---

## 6. Estrategia de Ejecución: Slices Verticales (Tracer Bullets)

A partir de este punto, la metodología abandona radicalmente el enfoque de cascada horizontal tradicional. Está **estrictamente prohibido** construir toda la base de datos, luego entrenar todo el modelo, y finalmente construir todo el frontend.

Dado que los agentes de IA rinden exponencialmente mejor con contextos cerrados y acotados, las fases subsiguientes (*Engineering, Modeling, Delivery*) se ejecutan de manera iterativa mediante **Slices Verticales (Balas Trazadoras)**:

1. **La Bala Trazadora (Iteración 0 - End-to-End):** El Backlog Manager selecciona **un solo** caso de uso o variable crítica del BDD. El equipo construye el pipeline de datos mínimo, entrena un modelo base (incluso un *dummy model*) y lo expone en una UI/API simple. El objetivo es validar la arquitectura profunda y la conexión de todo el sistema.
2. **Expansión Horizontal (Iteraciones 1..N):** Una vez que la "bala trazadora" arroja GREEN en el test E2E, se procede a agregar gradualmente el resto de las variables, complejizar el algoritmo (MLOps) y expandir el frontend.

*Nota: Las secciones a continuación describen los dominios técnicos (Engineering, Modeling, Delivery), pero en la práctica, los agentes de estas fases trabajan de forma intercalada por cada "Slice".*

---

> **Resultado Final de la Phase Discovery:** Un repositorio con la documentación necesaria para que el equipo de ingeniería empiece a construir la primera bala trazadora, sabiendo exactamente qué construir y cómo será evaluado.




# Phase Engineering: Data Ingestion, Engineering & Quality Assurance (DI&E)

Esta fase constituye la columna vertebral técnica del proyecto. Es el proceso de transformación de la "materia prima" (datos en crudo) en un **Feature Set** de alta fidelidad, aplicando principios de ingeniería de software de misión crítica. En esta etapa, el **SAD** y el **SpecDD** de la Phase Discovery se materializan en código modular, testeado y certificado, integrando el **Análisis Exploratorio de Datos (EDA)** como el sensor principal de calidad en cada etapa.

---

## 1. Definición de la Fase: El Pipeline de Producción y el Rol del EDA
En la industria, el pipeline no es una caja negra; es un flujo transparente basado en la **Medallion Architecture** (Bronze, Silver, Gold). El **Análisis Exploratorio de Datos (EDA)** deja de ser una tarea única y se convierte en un proceso recurrente que alimenta la toma de decisiones técnicas y la creación de pruebas automatizadas.

---

## 2. El Equipo de Agentes: Roles de Ingeniería de Datos

### A. AI Data Engineer (El Arquitecto de Pipelines e Ingesta)
**Nombre:** ai-data-engineer
**Misión:** Construir y mantener la infraestructura de transporte y almacenamiento, garantizando la fidelidad del dato original.
* **Funciones Detalladas:**
    * **Extracción Multi-modal:** Configura conectores de alta eficiencia para diversas fuentes (SQL/NoSQL, APIs REST/gRPC, Streams).
    * **Implementación de la Capa Bronze:** Carga de datos inmutables con metadatos de linaje.
    * **EDA Técnico de Ingesta (Post-Ingesta):** Realiza un perfilado técnico inmediato para validar la integridad del transporte. Verifica conteos de registros, integridad de tipos de datos, errores de codificación (UTF-8) y detección de esquemas rotos.
    * **Seguridad y Gobernanza:** Implementa cifrado en reposo y tránsito, y gestiona el acceso a datos sensibles (PII).

### B. AI Analytics Engineer (El Especialista en Transformación y Calidad)
**Nombre:** ai-analytics-engineer
**Misión:** Modelar y limpiar los datos para convertirlos en información estructurada y veraz.
* **Funciones Detalladas:**
    * **Capa Silver (Cleansed Data):** Desarrolla los módulos `.py` de limpieza y normalización bajo **SpecDD**.
    * **EDA de Transformación y Limpieza:** Analiza las distribuciones estadísticas antes y después de aplicar reglas de limpieza. Su objetivo es detectar si el tratamiento de nulos o la eliminación de duplicados está sesgando artificialmente la realidad del negocio.
    * **Imputación de Datos:** Aplica la lógica técnica para el tratamiento de valores faltantes basada en hallazgos del Auditor de Datos de la Phase Discovery.

### C. AI Feature Store Architect (El Escultor de Variables y Estadística)
**Nombre:** ai-feature-store-architect
**Misión:** Diseñar y generar las variables predictivas ($X$) que maximizan el poder del modelo.
* **Funciones Detalladas:**
    * **Feature Engineering Complejo:** Crea nuevas variables (funciones de ventana, agregaciones, ratios).
    * **EDA Estadístico (Capa Gold):** Realiza análisis de correlación (Multicolinealidad), análisis de varianza y detección de *Target Leakage*. Valida que la relación entre las variables independientes ($X$) y la variable objetivo ($y$) sea estadísticamente significativa.
    * **Pre-procesamiento Determinístico:** Implementa escaladores ($Scaling$) y codificadores ($Encoding$) siguiendo el contrato del **SpecDD**.

### D. AI Data SDET / QA Engineer (El Guardián de la Integridad y TDD)
**Nombre:** ai-data-qa-engineer
**Misión:** Garantizar que ningún dato que no cumpla con el **Contrato de Datos** avance en el pipeline. Es el responsable de orquestar el ciclo de calidad.
* **Funciones Detalladas:**
    * **Diseño de Casos de Prueba:** Traduce los hallazgos de los tres momentos del EDA en tests de validación automatizados.
    * **Validación de Esquemas:** Implementa validadores estrictos (Pydantic / Great Expectations).
    * **Certificación Técnica:** Verifica el cumplimiento de estándares de código, logs y manejo de excepciones definidos en el **SAD**.
---


## 3. Los Tres Momentos Críticos del EDA en la Phase Engineering

El EDA no es un evento; es una capacidad de diagnóstico que se aplica en tres puntos de control:

| Momento del EDA           | Nivel de Datos | Agente Líder       | Propósito Técnico                                                                                                                     |
| :------------------------ | :------------- | :----------------- | :------------------------------------------------------------------------------------------------------------------------------------ |
| **EDA de Ingesta**        | Capa Bronze    | Data Engineer      | Detectar fallos en el transporte, truncamiento de datos y discrepancias de esquemas.                                                  |
| **EDA de Transformación** | Capa Silver    | Analytics Engineer | Validar que la limpieza no elimine señales valiosas y que la imputación mantenga la distribución original.                            |
| **EDA Estadístico**       | Capa Gold      | Feature Architect  | Identificar el poder predictivo de las variables y asegurar que no haya "fuga" de información del objetivo ($Target\text{ }Leakage$). |

---


## 4. Metodología TDD Aplicada: El Ciclo de Calidad Total

En esta fase, la creación de cada módulo `.py` (ej. `imputacion.py`, `limpieza.py`) sigue obligatoriamente este ciclo:

1.  **RED (Falla Inicial):** Basado en el escenario específico del **BDD (`behavior.md`)** y el **SpecDD**, el **AI Data SDET** escribe un test automatizado (unitario o E2E) que define el comportamiento esperado. Al no existir el código del módulo, el test falla. Esto asegura que la meta técnica está alineada con el negocio.
2.  **GREEN (Pasa el Test - DoD Absoluto):** El **AI Analytics Engineer** escribe el código mínimo necesario en el archivo `.py` para que el test pase. **El agente no puede marcar la tarea como DONE ni solicitar revisión hasta que el test del escenario BDD arroje `GREEN`.** El BDD es el DoD absoluto.
3.  **REFACTOR (Optimización):** Se mejora el código del módulo para aumentar la eficiencia (ej. vectorización con NumPy/Pandas en lugar de bucles), mejorar la legibilidad y añadir documentación técnica, asegurando que el test siga en verde.
4.  **CERTIFICACIÓN (Cumplimiento de Especificación):** El **AI Solutions Architect** revisa que el módulo se integre correctamente con el **SAD**. Se certifica que el módulo maneja excepciones y logs de manera estandarizada.
5.  **VALIDACIÓN (Calidad de Datos):** El **AI Data SDET** realiza una validación estadística. No solo se verifica que el código "corra", sino que los datos resultantes sean coherentes (ej. que las distribuciones no se hayan sesgado artificialmente durante la imputación).

---

## 5. Implementación del Flujo de Trabajo (Por cada Slice Vertical)

*Nota: Este flujo NO se ejecuta para el 100% de la base de datos de una sola vez, sino que se repite para la variable o componente asignado en el Tracer Bullet actual.*

1.  **Carga y Perfilado (Bronze):** Ingesta del fragmento de datos necesario y ejecución del **EDA de Ingesta**.
2.  **Desarrollo Modular Aislado:** Creación o actualización del script `.py` correspondiente a esa única variable, operando bajo estricto Aislamiento de Contexto.
3.  **Puerta de Control Silver:** El **EDA de Transformación** valida el módulo de limpieza.
4.  **Generación de Feature:** Construcción de la Capa Gold para esa variable y ejecución del **EDA Estadístico**.
5.  **Orquestación Iterativa:** Integración del nuevo módulo en el `main.py` de ingeniería.
6.  **Certificación Final:** Ejecución de la suite de tests (E2E y unitarios) para garantizar que el nuevo Slice está en GREEN sin romper los Slices anteriores.

---

## 6. Artefactos y Entregables Técnicos

| Artefacto                      | Responsable             | Ruta de Almacenamiento    | Detalle Técnico                                                              |
| :----------------------------- | :---------------------- | :------------------------ | :--------------------------------------------------------------------------- |
| **Data Pipeline Orchestrator** | AI Data Engineer        | `src/`                    | Código del `main.py` y configuración del orquestador (Airflow/Prefect).      |
| **Library of Modules (.py)**   | Analytics Engineer      | `src/`                    | Scripts testeados de limpieza, transformación e ingeniería de variables.     |
| **EDA & Profiling Reports**    | Todos los agentes       | `docs/Phase_engineering/` | Tres informes de diagnóstico (Ingesta, Transformación y Estadístico).        |
| **Automated Test Suite**       | AI Data SDET            | `tests/`                  | Repositorio de tests unitarios e integrales (Pytest / Great Expectations).   |
| **Feature Store / Gold Layer** | Feature Store Architect | `data/gold/`              | Tablas finales certificadas, optimizadas y versionadas.                      |
| **Updated SAD (Data View)**    | AI Solutions Architect  | `docs/Phase_engineering/` | Mapa de linaje de datos y diagrama de componentes de ingeniería actualizado. |

---

> **Resultado Final de la Phase Engineering:** Una factoría de datos automatizada, auditable y estadísticamente validada. Se entregan módulos de código robustos que garantizan que el Científico de Datos en la Phase Modeling trabaje sobre una base sólida de datos "Gold", habiendo superado el rigor del ciclo **Red, Green, Refactor, EDA, Certificación y Validación**.




# Phase Modeling: Model Development, Experimentation & ML Engineering

Esta fase representa el motor analítico del proyecto. Aquí es donde la "Capa Gold" generada en la Phase Engineering se utiliza para entrenar, optimizar y validar los modelos de Machine Learning. Siguiendo tu flujo de trabajo, la experimentación ocurre en entornos flexibles (Notebooks), pero la implementación final se traduce en **módulos `.py` altamente estructurados**, orquestados por el `main.py` y bajo el rigor absoluto de la metodología **TDD**.

---

## 1. Definición de la Fase: R&D Aligned with Production
En la industria actual, el modelado no es un proceso aislado de "ensayo y error". Es una fase de **Ingeniería de Modelos** donde la reproducibilidad es ley. Se utilizan herramientas de rastreo de experimentos (como MLflow o Weights & Biases) para asegurar que cada versión del modelo, cada hiperparámetro y cada métrica de rendimiento queden registrados en el **SAD**.

---

## 2. El Equipo de Agentes: Roles de Modelado y Algoritmia

### A. AI Data Scientist (El Investigador de Hipótesis)
**Nombre:** ai-data-scientist
**Misión:** Encontrar el algoritmo y la configuración óptima para resolver el problema planteado por el Estratega en la Phase Discovery.
* **Funciones Detalladas:**
    * **Selección de Algoritmos:** Evalúa arquitecturas (Gradient Boosting, Deep Learning, Transformers) basándose en la naturaleza de los datos.
    * **Optimización de Hiperparámetros:** Utiliza técnicas avanzadas (Bayesian Optimization u Optuna) para maximizar las métricas de rendimiento.
    * **Análisis de Importancia de Variables:** Identifica qué características de la "Capa Gold" están impulsando las predicciones.
    * **Entrenamiento de Baseline:** Crea el primer modelo de referencia para establecer el punto de partida del ciclo TDD.

### B. AI ML Engineer (El Arquitecto de Modelos en Producción)
**Nombre:** ai-ml-engineer
**Misión:** Traducir los hallazgos del Data Scientist en código de grado de producción (.py) modular y eficiente.
* **Funciones Detalladas:**
    * **Industrialización del Modelo:** Encapsula el modelo en clases de Python siguiendo el **SpecDD** (firmas de métodos `.fit()` y `.predict()`).
    * **Serialización y Versionado:** Implementa el guardado de modelos en formatos estándares (ONNX, Pickle, Joblib) dentro del sistema de archivos o Model Registry.
    * **Optimización de Inferencia:** Asegura que el modelo responda en los tiempos de latencia definidos en el **SAD**.
    * **Refactorización de Código:** Limpia el código experimental de los Notebooks para integrarlo en el pipeline principal.

### C. AI MLOps Specialist (El Ingeniero de Ciclo de Vida)
**Nombre:** ai-mlops-specialist
**Misión:** Garantizar la trazabilidad, el versionado y la infraestructura necesaria para el entrenamiento.
* **Funciones Detalladas:**
    * **Experiment Tracking:** Configura el sistema de registro de métricas y artefactos.
    * **Model Registry Management:** Gestiona las versiones de los modelos (Staging, Production, Archived).
    * **Infrastructure Scalability:** Asegura que el proceso de entrenamiento tenga los recursos necesarios (GPU/CPU/Memoria) según lo dictado por el **SAD**.
    * **Data/Model Lineage:** Vincula qué versión de los datos "Gold" produjo qué versión del modelo.

### D. AI Model QA & Validator (El Auditor de Rendimiento y Ética)
**Nombre:** ai-model-qa-validator
**Misión:** Ejecutar el ciclo **TDD** aplicado a modelos. Validar no solo el código, sino la "inteligencia" del modelo.
* **Funciones Detalladas:**
    * **Benchmarking:** Verifica que el nuevo modelo supere los criterios de aceptación mínimos definidos en la Phase Discovery.
    * **Bias & Fairness Testing:** Audita el modelo para asegurar que no existan sesgos discriminatorios hacia subgrupos de datos.
    * **Stress Testing (Robustness):** Evalúa cómo se comporta el modelo ante datos ruidosos o fuera de distribución (*Out-of-distribution*).

---

## 3. Metodología TDD Aplicada al Modelado: Ciclo de Inteligencia Certificada

El entrenamiento del modelo y la creación de su módulo de predicción (`modelo.py`) sigue este ciclo riguroso:

1.  **RED (Definición del Target):** El **AI Model QA** escribe un test de rendimiento basado en el **SpecDD**. Ejemplo: *"El test falla si el modelo no alcanza un F1-Score > 0.85"*. Como no hay modelo aún, el sistema está en rojo.
2.  **GREEN (Entrenamiento Exitoso):** El **AI Data Scientist** entrena una arquitectura que logra superar el umbral del test. El **AI ML Engineer** escribe el código mínimo para que la función `.predict()` devuelva resultados válidos. El test pasa a verde.
3.  **REFACTOR (Optimización de Código y Modelo):** El **AI ML Engineer** mejora la estructura del código y el **AI Data Scientist** realiza *Pruning* o destilación para que el modelo sea más ligero sin perder precisión. El test debe seguir en verde.
4.  **CERTIFICACIÓN (Cumplimiento de SAD):** El **AI Solutions Architect** certifica que el modelo cumple con los requisitos de la Phase Discovery: tamaño del archivo, tiempo de respuesta en milisegundos y dependencias de librerías permitidas.
5.  **VALIDACIÓN (Generalización y Negocio):** Se realiza una validación cruzada ($Cross\text{-}Validation$) y se evalúa el modelo contra un *Hold-out set* (datos que el modelo jamás ha visto). El **AI Business Analyst** valida que el impacto en el KPI de negocio sea el esperado.



---

## 4. Implementación del Flujo de Trabajo (Por cada Slice Vertical)

*Nota: El modelado se ejecuta iterativamente acoplado al Tracer Bullet actual, priorizando modelos "dummy" o base en las primeras iteraciones para validar la arquitectura.*

1.  **Experimentación (Notebooks):** El **Data Scientist** trabaja en `.ipynb` aislando su contexto solo a la variable/componente del Tracer Bullet.
2.  **Modularización Aislada (.py):** El **ML Engineer** crea el módulo final de entrenamiento y predicción, recibiendo en su prompt únicamente su script y el test asociado.
3.  **Registro de Artefactos:** El **MLOps Specialist** registra el modelo (o baseline) resultante.
4.  **Orquestación Iterativa:** El `main.py` integra la fase de entrenamiento de este slice específico.
5.  **Puerta de QA (BDD-as-DoD):** El **Model QA** certifica que el nuevo módulo arroja GREEN en el test asociado al escenario BDD.

---

## 5. Artefactos y Entregables Técnicos

| Artefacto                     | Responsable            | Ruta de Almacenamiento | Detalle Técnico                                                                                   |
| :---------------------------- | :--------------------- | :--------------------- | :------------------------------------------------------------------------------------------------ |
| **Model Codebase (.py)**      | AI ML Engineer         | `src/`                 | Módulos de entrenamiento, evaluación e inferencia bajo **SpecDD**.                                |
| **Experiment Log / Registry** | AI MLOps Specialist    | `mlruns/` (o eq)       | Historial completo de parámetros, métricas y versiones del modelo.                                |
| **Serialized Model File**     | AI ML Engineer         | `models/`              | El artefacto final (ONNX, Pickle, etc.) listo para ser consumido por la API.                      |
| **Model Validation Report**   | AI Model QA            | `docs/Phase_modeling/` | Informe de performance, análisis de errores, pruebas de sesgo y robustez.                         |
| **Updated SAD (Model View)**  | AI Solutions Architect | `docs/Phase_modeling/` | Documentación de la arquitectura del modelo, hiperparámetros finales y métricas de SLA.           |
| **Notebooks de R&D**          | AI Data Scientist      | `notebooks/`           | Archivos `.ipynb` documentados que explican el proceso de descubrimiento y descarte de hipótesis. |

---

> **Resultado Final de la Phase Modeling:** Un modelo de Machine Learning certificado, robusto y serializado. No es solo un archivo de pesos; es un componente de software testeado bajo el ciclo **Red, Green, Refactor, Certificación y Validación**, listo para ser integrado en el **Backend** (Phase Delivery) y servido al cliente final.




# Phase Delivery: Software Application, Integration & Deployment (SAI&D)

Esta fase representa el clímax técnico del proyecto, donde el modelo de Machine Learning y los pipelines de datos dejan de ser artefactos de laboratorio para convertirse en un **Producto de Software de Grado Industrial**. Aquí, la inteligencia se encapsula en una interfaz funcional, escalable y segura, diseñada para el consumo final del cliente. Se opera bajo una arquitectura desacoplada, garantizando que cada componente cumpla con el **SAD** y supere el ciclo de calidad **Red, Green, Refactor, Certificación y Validación**.

---

## 1. Definición de la Fase: Productivización y Entrega Continua
En esta etapa, el enfoque se desplaza de la precisión del modelo hacia la **disponibilidad, latencia y experiencia de usuario (UX)**. Se construye el ecosistema que permite que el modelo "respire" en el mundo real, manejando peticiones concurrentes, gestionando errores de red y presentando resultados de forma que el negocio pueda tomar decisiones inmediatas.

---

## 2. El Equipo de Agentes: Roles de Desarrollo y Operaciones

### A. AI Backend Engineer (El Arquitecto de APIs y Lógica de Servidor)
**Nombre:** ai-backend-engineer
**Misión:** Construir el motor robusto que sirve el modelo y gestiona la comunicación entre la base de datos y la interfaz.
* **Funciones Detalladas:**
    * **Desarrollo de API de Alta Performance:** Crea los endpoints (usualmente con FastAPI o gRPC) para recibir datos crudos, invocar los módulos de la Phase Engineering (limpieza/imputación) y ejecutar la inferencia del modelo de la Phase Modeling.
    * **Gestión de Concurrencia y Asincronía:** Implementa colas de mensajes (Redis/RabbitMQ) y tareas asíncronas (Celery) para procesos pesados, evitando bloqueos en la experiencia del usuario.
    * **Validación de Contratos de Entrada (SpecDD):** Implementa el *Schema Enforcement* estricto para rechazar datos corruptos antes de que toquen el modelo.

### B. AI Frontend Engineer (El Diseñador de Interfaz y UX Predictiva)
**Nombre:** ai-frontend-engineer
**Misión:** Crear la cara visible del proyecto, traduciendo la complejidad matemática en una herramienta intuitiva.
* **Funciones Detalladas:**
    * **Desarrollo de Dashboard Interactivo:** Construye la UI (Streamlit para prototipos o React/Next.js para producción) permitiendo la carga de datos y visualización de resultados.
    * **Visualización de Explicabilidad (XAI):** Implementa componentes visuales que explican el "por qué" de una predicción (ej. gráficos SHAP o semáforos de confianza).
    * **Gestión de Estado y Feedback:** Diseña flujos para que el usuario pueda corregir datos o reportar predicciones erróneas, cerrando el ciclo de feedback.

### C. AI MLOps & Cloud Architect (El Ingeniero de Infraestructura y Escala)
**Nombre:** ai-mlops-cloud-architect
**Misión:** Garantizar que la aplicación viva en un entorno resiliente, automatizado y seguro.
* **Funciones Detalladas:**
    * **Contenedorización Total:** Empaqueta backend, frontend y modelos en imágenes de Docker optimizadas.
    * **Diseño de CI/CD Pipelines:** Automatiza el despliegue para que cualquier mejora en el código o reentrenamiento del modelo llegue a producción sin intervención manual.
    * **Estrategia de Escalabilidad:** Configura el auto-escalado en la nube para manejar picos de demanda y optimizar costos operativos.

### D. AI Full-Stack SDET (El Validador del Sistema de Punto a Punto)
**Nombre:** ai-full-stack-sdet
**Misión:** Aplicar la metodología **TDD** a nivel de integración total. Certifica que el "todo" es funcional.
* **Funciones Detalladas:**
    * **End-to-End (E2E) Testing:** Crea scripts que simulan un usuario real cargando un archivo y verificando que la gráfica final sea correcta. Cada test E2E valida específicamente un escenario Gherkin definido en `docs/governance/behavior.md`: el Happy Path, la advertencia de baja confianza y el rechazo de entradas fuera de rango. El **[[behavior]].md** es la fuente de verdad para la cobertura E2E.
    * **Load & Stress Testing:** Somete a la API a cargas masivas para identificar el punto de ruptura.
    * **Validación de Seguridad:** Realiza pruebas de penetración básicas y asegura que no haya fugas de datos sensibles en los logs.

---

## 3. Metodología TDD Aplicada: El Ciclo de Lanzamiento Certificado

Cada funcionalidad de la aplicación (ej. el botón de "Predecir") sigue este ciclo riguroso:

1.  **RED (Test de Integración):** El **AI SDET** escribe un test que intenta obtener una predicción a través de la interfaz web. El test falla porque la API aún no está conectada al frontend.
2.  **GREEN (Funcionalidad Mínima):** El **AI Backend** expone el modelo y el **AI Frontend** realiza la llamada `fetch`. El test pasa: la aplicación devuelve un número.
3.  **REFACTOR (Optimización de Software):** Se mejora el manejo de errores (ej. mostrar un "Spinner" de carga), se optimiza el tamaño de los paquetes de datos y se asegura que el código siga los estándares del **SAD**.
4.  **CERTIFICACIÓN (SAD & Security Compliance):** El **AI Solutions Architect** certifica que la aplicación cumple con los protocolos de seguridad (SSL/HTTPS, OAuth) y que la latencia cumple el SLA definido.
5.  **VALIDACIÓN (UAT - User Acceptance Testing):** El **AI Business Analyst** (Phase Discovery) valida con el cliente que la herramienta realmente resuelve la necesidad operativa inicial.



---

## 4. El Análisis Exploratorio de Datos (EDA) en la Phase Delivery

En esta etapa final, el EDA evoluciona hacia el **Perfilado de Producción**:

| Tipo de EDA                          | Objetivo                                                | Agente Líder         | Resultado                                                   |
| :----------------------------------- | :------------------------------------------------------ | :------------------- | :---------------------------------------------------------- |
| **EDA de Performance**               | Analizar tiempos de respuesta de cada módulo en la red. | AI Backend Engineer  | Identificación de cuellos de botella en la API.             |
| **EDA de Comportamiento de Usuario** | Medir qué funcionalidades de la App son más usadas.     | AI Frontend Engineer | Ajustes en el diseño para mejorar la usabilidad.            |
| **EDA de Inferencia Real**           | Perfilado de los datos que llegan "del mundo real".     | AI SDET / MLOps      | Detección de *Data Drift* (datos reales vs. entrenamiento). |

---

## 5. Implementación del Flujo de Trabajo (Por cada Slice Vertical)

*Nota: La UI/API se construye de forma incremental, garantizando desde la primera iteración que los datos del Tracer Bullet cruzan con éxito hasta la pantalla del usuario.*

1.  **Integración Sistémica Aislada:** El **Backend Engineer** importa el módulo certificado del slice actual y expone el endpoint necesario respetando el diseño de "Módulos Profundos".
2.  **Despliegue de Interfaz:** El **Frontend Engineer** crea los componentes visuales estrictamente necesarios para ese escenario BDD.
3.  **Orquestación de Producción:** El `main.py` de la aplicación conecta el pipeline desde la ingesta hasta la interfaz.
4.  **Puerta de QA Final (BDD-as-DoD):** Se ejecutan los tests E2E. Si el escenario BDD arroja GREEN de punta a punta, el slice se considera finalizado y el **MLOps Engineer** puede desplegarlo de manera continua.

---

## 6. Artefactos y Entregables Técnicos

| Artefacto                             | Responsable            | Ruta de Almacenamiento | Detalle Técnico                                                         |
| :------------------------------------ | :--------------------- | :--------------------- | :---------------------------------------------------------------------- |
| **Production API (Backend)**          | AI Backend Engineer    | `src/`                 | Repositorio de código con documentación OpenAPI/Swagger.                |
| **Web Application (Frontend)**        | AI Frontend Engineer   | `src/`                 | Interfaz de usuario productiva y manual de usuario técnico.             |
| **Infraestructura como Código (IaC)** | AI MLOps Architect     | `infra/`               | Scripts de despliegue (Terraform/Docker Compose/K8s manifests).         |
| **E2E & Load Test Report**            | AI SDET                | `docs/Phase_delivery/` | Certificado de resistencia y correcto funcionamiento sistémico.         |
| **Monitoring Dashboard**              | AI MLOps Architect     | `docs/Phase_delivery/` | Panel de control (Grafana/Prometheus) para vigilar la salud del modelo. |
| **SAD Finalizado (As-Built)**         | AI Solutions Architect | `docs/Phase_delivery/` | Documentación final de la arquitectura tal como quedó desplegada.       |

---

> **Resultado Final de la Phase Delivery:** Una solución de IA integral, robusta y accesible. No es solo un modelo; es un producto de software certificado que ha superado el ciclo **Red, Green, Refactor, EDA, Certificación y Validación**, garantizando que el valor de la ciencia de datos llegue intacto a las manos del cliente.

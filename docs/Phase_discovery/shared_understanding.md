# Shared Understanding Log: Flores_CD

Este documento registra la entrevista socrática para definir los requerimientos del proyecto. Es la base inmutable para la redacción del BRD.

- **Fecha de entrevista:** 2026-04-28
- **Estado:** ✅ Firmado y aprobado

---

## Historial de Interrogación

| # | Pregunta | Respuesta del Usuario | Recomendación / Contexto |
| :--- | :--- | :--- | :--- |
| **1** | Descríbeme el problema de negocio que quieres resolver. ¿Cuál es la situación actual y qué decisión o acción concreta debería mejorar o automatizar este proyecto? | El cliente quiere construir un dashboard que le ayude a clasificar la flor Iris en una de sus tres especies. Actualmente lo hacen de forma manual, lo que genera demasiados reprocesos en la clasificación y baja en la confianza de los resultados por las dudas que presenta cada analista. | El problema central es la clasificación manual de especies de Iris con alto error humano. El proyecto apunta a automatizar esa clasificación mediante ML. Taxonomía tentativa: **clasificación multiclase supervisada** (3 clases: *Iris setosa*, *Iris versicolor*, *Iris virginica*). |
| **2** | Cuando el sistema clasifica incorrectamente una especie de Iris, ¿cuál es el impacto concreto para el cliente? ¿Se reinicia un proceso, se descarta una muestra, se genera un reporte incorrecto, o hay consecuencias regulatorias? | Hoy un analista realiza una predicción, que después es revisada por otro analista. Si los dos coinciden, el análisis se da por cerrado (aunque ya costó dos analistas). Cuando no coinciden, se reúne un comité de tres expertos para reclasificar y tomar una decisión. Esto causa reprocesos e inversión de tiempo muy costoso. | El problema real no es solo "clasificar bien", sino **reducir la fricción operativa del proceso de revisión**. El modelo debe entregar alta confianza en sus predicciones para evitar los casos de desacuerdo que activan el comité. Métrica objetivo preliminar: **Accuracy + F1-score macro**. |
| **3** | ¿Cuántas clasificaciones se realizan aproximadamente por semana o por mes, y cuántas de ellas terminan en desacuerdo (activando el comité de expertos)? | Son 5 agentes. Cada uno tiene asignado 3 análisis/hora y 3 revisiones/hora, de lunes a viernes de 8am–12pm y 2pm–6pm (8 horas/día). Por lo general, **1 de cada revisión termina en desacuerdo** y activa el comité, lo que impide cumplir la meta de 3 análisis/hora. | **ROI cuantificado:** 600 análisis/semana + 600 revisiones/semana → 600 activaciones de comité/semana → ~200 horas-agente perdidas en arbitraje. Una reducción del 60% en desacuerdos liberaría **~360 análisis adicionales por semana** sin incremento de personal. El ROI no es mejora de precisión: es **capacidad operativa recuperada**. |
| **4** | El dataset Iris contiene medidas de flores (longitud y ancho de sépalo y pétalo). ¿Estas medidas son exactamente los datos que los agentes registran diariamente, o el dataset Iris es solo un ejercicio técnico y el problema real involucra otras variables? | Es un **ejercicio técnico de aprendizaje con todo el rigor**, basado en el dataset de Fisher para el problema de clasificación de Iris. Es muy importante para que el cliente entienda su funcionamiento y pueda extender este tipo de trabajos a otras flores. | Alcance definido: el dataset de referencia es el **Iris de Fisher** (150 registros, 4 features: sepal_length, sepal_width, petal_length, petal_width; 3 clases balanceadas). El proyecto tiene doble propósito: (1) resolver el problema de clasificación, y (2) **servir como plantilla replicable** para otros proyectos de clasificación botánica del cliente. Esto implica que la arquitectura debe ser modular y bien documentada. |
| **5** | ¿Cuál es el nivel mínimo de F1-score macro que el cliente considera aceptable para declarar el modelo como "listo para producción"? ¿Se requieren métricas adicionales? | Se aprueba el threshold recomendado de **F1-score macro ≥ 0.95**. También se aprueban las métricas complementarias propuestas. | Set de 4 métricas aprobadas: (1) **F1-score macro ≥ 0.95** (métrica primaria), (2) **Accuracy ≥ 0.95** (comunicación con stakeholders), (3) **Confusion Matrix — Recall por clase ≥ 0.90** (detectar confusión versicolor/virginica), (4) **Advertencia de confianza si max(prob) < 0.60** (mecanismo operativo para reducir escalaciones al comité). AUC-ROC multiclase excluida por no aportar valor operativo adicional con dataset balanceado. |
| **6** | ¿Cómo interactúa el analista con el dashboard? ¿Ingreso manual, carga de CSV o API REST? ¿El dashboard muestra solo la predicción final o también probabilidades por clase? | Se aprueba la **opción A (ingreso manual)** según la recomendación. | Modalidad: el analista ingresa manualmente las 4 medidas (sepal_length, sepal_width, petal_length, petal_width) en un formulario y presiona "Clasificar". Outputs: (1) especie predicha, (2) probabilidades por clase (ej: "virginica 88%, versicolor 10%, setosa 2%"), (3) **advertencia visual** cuando max(prob) < 0.60. Descartadas modalidades B (CSV) y C (API) por no corresponder al caso de uso operativo individual. |
| **7** | Cuando el modelo advierte baja confianza (max prob < 0.60), ¿qué acción toma el analista: (A) escala al comité, (B) descarta la muestra, o (C) registra como "revisión pendiente" para confirmación posterior? | **Opción C**: registrar la predicción marcada como *"revisión pendiente"* para que un segundo analista confirme después. | Esto preserva el trabajo realizado, evita convocar al comité de 3 expertos para casos de baja confianza, y reduce el costo operativo identificado en la Pregunta 3. Implicación de arquitectura: el dashboard requiere un estado de predicción **"pendiente"** con flujo de confirmación por segundo analista, sin activar el comité completo. |
| **8** | ¿Hay alguna restricción tecnológica que invalide el stack por defecto (Python + scikit-learn + Streamlit, ejecución local en navegador web estándar)? Por ejemplo: lenguaje obligatorio, requisito cloud/on-premise, o plataforma específica. | **Sin restricciones.** | Stack tecnológico definitivo aprobado: **Python + scikit-learn + Streamlit**, ejecutable localmente, accesible desde cualquier navegador web estándar (Chrome, Firefox, Edge). Sin dependencias de infraestructura cloud ni servidores externos. |
| **9** | Cuando el modelo marca una predicción como "revisión pendiente", ¿debe quedar guardada para que un segundo analista la vea en una sesión posterior, o el flujo de confirmación ocurre dentro de la misma sesión activa? | De acuerdo con la recomendación: **persistir historial local**. | Mecanismo de persistencia aprobado: **archivo CSV o SQLite local**. Las predicciones de baja confianza quedan registradas con estado "pendiente" y cualquier analista puede abrir el dashboard en cualquier momento para verlas y confirmarlas. Implicación de arquitectura: el dashboard requiere una capa de persistencia ligera (lectura/escritura de archivo local) y una vista de "cola de revisiones pendientes". Coherente con el stack Streamlit local sin infraestructura adicional. |

---

## Conclusiones Clave del Entendimiento Compartido

| Dimensión | Hallazgo |
| :--- | :--- |
| **Tipo de problema ML** | Clasificación multiclase supervisada (3 clases) |
| **Dataset** | Iris de Fisher — 150 registros, 4 features numéricas |
| **Entregable principal** | Dashboard interactivo para clasificación de flores |
| **Usuarios del sistema** | Analistas del cliente (5 agentes operativos) |
| **Métrica de éxito de negocio** | Reducción de activaciones del comité de expertos (actualmente ~100% de revisiones) |
| **Métricas técnicas aprobadas** | F1-score macro ≥ 0.95 · Accuracy ≥ 0.95 · Recall por clase ≥ 0.90 · Advertencia confianza < 0.60 |
| **Modalidad del dashboard** | Ingreso manual de 4 medidas + predicción + probabilidades por clase + advertencia visual |
| **Flujo baja confianza** | Predicción marcada como "revisión pendiente" → confirmación por segundo analista (sin comité) |
| **Persistencia del estado** | Historial local en CSV/SQLite · Cola de revisiones pendientes accesible por cualquier analista |
| **Propósito extendido** | Plantilla replicable para clasificación de otras especies |
| **Naturaleza del proyecto** | Ejercicio técnico riguroso con impacto operativo real |
| **Stack tecnológico aprobado** | Python + scikit-learn + Streamlit · Ejecución local · Navegador web estándar · Sin restricciones cloud/on-premise |

---

## Firmas

| Rol | Nombre | Fecha |
| :--- | :--- | :--- |
| **Usuario / Product Owner** | jdrodriguez1000 | 2026-04-28 |
| **Agente Responsable** | `ai-business-strategist` | 2026-04-28 |

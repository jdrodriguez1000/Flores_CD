---
name: ci-cd-pipeline-automated-designer
description: Protocolo para la automatización de flujos de integración y despliegue continuo (CI/CD) integrando pruebas técnicas y de modelos.
user-invocable: false
agent: ai-mlops-cloud-architect
allowed-tools: [Read, Write, Bash, Edit]
---

## 🏗️ I. Configuración de Integration (CI)
El agente debe automatizar la validación de cada cambio:
1. **Automated Testing:** Ejecutar la suite de tests unitarios y técnicos definidos por el QA Engineer en cada Pull Request.
2. **Model Validation Hook:** Disparar validaciones de rendimiento del modelo si el cambio afecta a los pesos o arquitectura de la IA.
3. **Linting & Quality:** Bloquear despliegues si el código no cumple con los estándares de estilo o seguridad.

## 📐 II. Estrategia de Deployment (CD)
1. **Blue/Green o Canary Deployment:** Implementar estrategias de despliegue progresivo para minimizar el impacto ante fallas en producción.
2. **Automated Tagging:** Generar versiones automáticas de las imágenes de Docker vinculadas al commit de Git.
3. **Rollback Automático:** Configurar el pipeline para volver a la versión anterior si el healthcheck de la nueva versión falla.

## 🚀 III. Notificaciones y Trazabilidad
1. **Alerting de Pipeline:** Notificar al equipo (Slack/Teams) sobre el éxito o fallo de los despliegues.
2. **Registro de Auditoría:** Mantener un log inmutable de quién desplegó qué versión y en qué momento.

---

> **Check de Certificación de CI/CD:**
> - [ ] ¿El despliegue a Staging/Producción ocurre sin intervención manual?
> - [ ] ¿Se ejecutan los tests de QA antes de cada despliegue?
> - [ ] ¿Existe un mecanismo de rollback probado y funcional?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

---
name: cloud-scalability-cost-optimzer
description: Protocolo para el diseño de infraestructura escalable en la nube y optimización de costos operativos mediante políticas de auto-escalado.
user-invocable: false
agent: ai-mlops-cloud-architect
allowed-tools: [Read, Write, Bash]
---

## 🏗️ I. Diseño de Arquitectura Cloud
El agente debe configurar el entorno de nube (AWS/GCP/Azure) según el SAD:
1. **Compute Scaling:** Implementar políticas de auto-escalado (HPA en Kubernetes o equivalentes) basadas en uso de CPU o número de peticiones.
2. **GPU Provisioning:** Si el modelo lo requiere, configurar pools de nodos con aceleración por hardware que se activen solo durante picos de inferencia pesada.
3. **High Availability:** Diseñar la infraestructura en múltiples zonas de disponibilidad para garantizar resiliencia ante caídas de proveedores.

## 📐 II. Gobernanza de Costos
1. **Right-sizing:** Ajustar el tamaño de las instancias para evitar el pago por recursos ociosos.
2. **Implementación de Spot Instances:** Usar instancias de bajo costo para tareas asíncronas no críticas o re-entrenamientos.
3. **Budget Alerts:** Configurar alertas de gasto para que el Estratega de Negocio sepa si el consumo de nube se desvía del presupuesto.

## 🚀 III. Monitorización y Observabilidad
1. **Cloud Metrics:** Configurar dashboards de infraestructura (CloudWatch/Stackdriver) para supervisar la salud de los servicios.
2. **Log Aggregation:** Centralizar los logs de todos los contenedores para facilitar el debug cross-service.

---

> **Check de Certificación de Escalabilidad:**
> - [ ] ¿La infraestructura escala automáticamente ante el incremento de carga?
> - [ ] ¿Se han implementado medidas de ahorro de costos (ej: apagado de entornos de desarrollo)?
> - [ ] ¿Es la arquitectura tolerante a fallos en una zona de disponibilidad?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

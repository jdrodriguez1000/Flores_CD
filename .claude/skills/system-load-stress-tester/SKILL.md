---
name: system-load-stress-tester
description: Protocolo para la ejecución de pruebas de carga y estrés masivo para identificar el punto de ruptura del sistema bajo alta demanda.
user-invocable: false
agent: ai-full-stack-sdet
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Definición de Perfiles de Carga
El agente debe simular diferentes niveles de tráfico:
1. **Load Test:** Verificar el comportamiento con el número de usuarios concurrentes esperado por el negocio.
2. **Stress Test:** Aumentar gradualmente la carga hasta encontrar el punto donde la API empieza a devolver errores 500 o tiempos de respuesta inaceptables.
3. **Soak Test:** Mantener una carga constante durante horas para detectar fugas de memoria (Memory Leaks) en el servidor de modelos.
4. **Documentación Obligatoria:** El Load Test Report debe guardarse en `docs/Phase_delivery/Load_Test_Report.md`.

## 📐 II. Ejecución con Herramientas de Carga (Locust/JMeter)
1. **Escenarios Concurrentes:** Simular usuarios haciendo peticiones de inferencia pesadas al mismo tiempo.
2. **Monitorización de Recursos:** Observar el uso de CPU/RAM del contenedor de la API y el MLOps Cloud Host durante la prueba.

## 🚀 III. Informe de Capacidad Técnica
1. **Identificación de Cuellos de Botella:** Determinar si el fallo es por base de datos, por el broker de mensajes o por el tiempo de computación del modelo.
2. **SLA Validation:** Certificar si el sistema cumple con la disponibilidad y latencia prometida bajo carga real.

---

> **Check de Certificación de Carga:**
> - [ ] ¿Se ha identificado el número máximo de predicciones por segundo que soporta el sistema?
> - [ ] ¿Se ha verificado la estabilidad del sistema tras 1 hora de carga constante?
> - [ ] ¿El reporte incluye recomendaciones de escalado basadas en los fallos detectados?
> - [ ] ¿El reporte está guardado en `docs/Phase_delivery/Load_Test_Report.md`?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

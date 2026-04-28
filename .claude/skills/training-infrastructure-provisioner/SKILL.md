---
name: training-infrastructure-provisioner
description: Protocolo para la gestión y escalabilidad de los recursos de cómputo (CPU, GPU, RAM) necesarios para el entrenamiento de modelos de IA.
user-invocable: false
agent: ai-mlops-specialist
allowed-tools: [Read, Write, Bash]
---

## 🏗️ I. Perfilado de Recursos de Entrenamiento
El agente debe dimensionar la infraestructura basándose en los algoritmos seleccionados:
1. **Memoria (RAM):** Asegurar que el dataset (Gold Layer) quepa en memoria o configurar cargadores de datos por lotes (Generators).
2. **Cómputo (CPU/GPU):** Verificar si el entrenamiento requiere aceleración por hardware (Cuda/Cudnn) y asegurar la disponibilidad de drivers correctos.
3. **Almacenamiento Temporal:** Garantizar espacio suficiente para checkpoints de modelos pesados durante el entrenamiento.

## 📐 II. Orquestación de Cargas de Trabajo (Jobs)
1. **Containerización (Docker):** Crear imágenes balanceadas con las versiones exactas de las librerías para evitar configuraciones "ad-hoc".
2. **Estimación de Costos:** Calcular el costo proyectado de los ciclos de entrenamiento y optimización de hiperparámetros.
3. **Escalabilidad Horizontal:** Si el dataset crece, configurar sistemas de entrenamiento distribuido (ej: Horovod o frameworks nativos).

## 🚀 III. Monitorización del Sistema
1. **Métricas de Infraestructura:** Supervisar el uso de GPU/CPU durante el entrenamiento para detectar infrautilización o falta de recursos.
2. **Alerting Técnico:** Disparar alertas si un proceso de entrenamiento se detiene por falta de memoria (OOM).

---

> **Check de Certificación de Infraestructura:**
> - [ ] ¿La imagen de contenedor utilizada es reproducible y está registrada?
> - [ ] ¿Los recursos asignados (VRAM/CPU) son suficientes para el entrenamiento sin causar fallas?
> - [ ] ¿Se ha verificado la compatibilidad de drivers de GPU en el entorno de ejecución?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

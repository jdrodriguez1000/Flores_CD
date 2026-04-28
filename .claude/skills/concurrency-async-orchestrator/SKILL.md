---
name: concurrency-async-orchestrator
description: Protocolo para la gestión de tareas asíncronas y concurrencia (Celery, Redis) para el procesamiento de IA de larga duración.
user-invocable: false
agent: ai-backend-engineer
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Identificación de Tareas Pesadas
El agente debe separar los flujos síncronos de los asíncronos:
1. **Sync Flow:** Predicciones unitarias rápidas que requieren respuesta inmediata.
2. **Async Flow:** Ingestas masivas, re-entrenamientos o procesamiento de archivos grandes que deben enviarse a una cola de mensajes.

## 📐 II. Implementación de Colas y Workers
1. **Message Broker (Redis/RabbitMQ):** Configurar el bus de comunicación entre la API y los workers de procesamiento.
2. **Workers de IA (Celery/RQ):** Implementar ejecuciones distribuidas que consuman las tareas de la cola sin bloquear la API principal.
3. **Persistencia de Tareas:** Implementar un sistema para que el usuario pueda consultar el estado de una tarea (`pending`, `started`, `success`, `failure`).

## 🚀 III. Gestión de Concurrencia a Nivel de Servidor
1. **Uvicorn/Gunicorn:** Configurar el número de workers y threads del servidor web según los recursos de CPU/Memoria disponibles.
2. **Control de Rate Limiting:** Implementar límites de peticiones para evitar la saturación del servidor por un solo usuario.

---

> **Check de Certificación de Concurrencia:**
> - [ ] ¿Las tareas que superan el timeout de la API han sido movidas a un flujo asíncrono?
> - [ ] ¿Se puede rastrear el estado y el resultado de cada tarea en segundo plano?
> - [ ] ¿El broker de mensajes tiene configurada persistencia ante reinicios?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

---
name: software-architecture-designer
description: Protocolo para el diseño de la topología del sistema, selección del stack tecnológico y elaboración del Software Architecture Document (SAD) para aplicaciones de IA.
user-invocable: false
agent: ai-solutions-architect
allowed-tools: [Read, Write, Edit, Bash]
---

## 🏗️ I. Diseño de Topología y Patrones
El agente debe definir la estructura del sistema basándose en la complejidad y escalabilidad requerida:
1. **Selección del Patrón y Módulos Profundos:** 
   - **Módulos Profundos (Deep Modules):** La arquitectura debe favorecer la creación de pocos módulos robustos con interfaces muy simples, en lugar de decenas de módulos pequeños (Shallow modules). Esto minimiza el acoplamiento y la carga cognitiva de los LLMs en las fases siguientes.
   - **Monolito Modular:** Para proyectos en etapa temprana o equipos pequeños, priorizando la cohesión.
   - **Microservicios/Micro-kernels:** Para sistemas que requieren escalabilidad independiente de módulos de inferencia.
   - **Arquitectura Hexagonal / Cebolla:** Obligatorio para desacoplar la lógica de negocio (modelos) de los detalles de infraestructura (APIs, DBs).
2. **Estructura del Proyecto:** Definir la jerarquía de carpetas siguiendo estándares de industria (ej: `src/`, `tests/`, `docs/`, `infra/`).

## 📐 II. Stack Tecnológico e Infraestructura
1. **Contenedorización (Docker):** Definir la estrategia de imágenes (Multi-stage builds) para optimizar el peso de las librerías de ML.
2. **Orquestación (Kubernetes/Compose):** Diseñar el despliegue de servicios concurrentes (Frontend, Backend, Model Serving, Celery Workers).
3. **Capa de Persistencia:** Determinar el uso de bases de datos según el tipo de dato:
   - **Relacionales (PostgreSQL):** Para metadatos, usuarios y logs estructurados.
   - **NoSQL / Vector DB:** Para almacenamiento de embeddings o datos no estructurados de alta velocidad.

## 🚀 III. Elaboración del Software Architecture Document (SAD)
El documento final debe seguir el **C4 Model** o similar, incluyendo:
1. **Selección del Stack Tecnológico:** Justificar la elección de lenguajes, frameworks y bases de datos según requisitos de latencia y volumen.
2. **Diagramación C4 Model:** Crear diagramas de Contexto, Contenedor y Componentes para visualizar la solución.
3. **Definición de Infraestructura:** Diseñar la topología (Cloud, On-premise, Híbrida) y servicios necesarios.
4. **Documentación Obligatoria:** El Software Architecture Document (SAD) debe guardarse en `docs/governance/sad.md`.

---

> **Check de Certificación Arquitectónica:**
> - [ ] ¿El diseño garantiza el desacoplamiento entre la lógica de ML (Phase Modeling) y la API (Phase Delivery)?
> - [ ] ¿Se ha definido una estrategia de escalabilidad para picos de inferencia?
> - [ ] ¿El SAD especifica claramente cómo se gestionarán los estados y la caché (Redis)?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

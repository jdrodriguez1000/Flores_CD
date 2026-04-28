---
name: full-stack-containerizer
description: Protocolo para la creación de imágenes de Docker optimizadas para Backend, Frontend y Modelos de IA, garantizando portabilidad y ligereza.
user-invocable: false
agent: ai-mlops-cloud-architect
allowed-tools: [Read, Write, Bash, Edit]
---

## 🏗️ I. Diseño de Dockerfiles Multi-Stage
El agente debe construir imágenes eficientes para cada componente:
1. **Separación de Capas:** Utilizar *Multi-stage builds* para separar el entorno de compilación (ej: npm install o pip install) del entorno de ejecución final.
2. **Minimización de Superficie:** Usar imágenes base ligeras (ej: `python:3.9-slim` o `alpine`) para reducir el tamaño y mejorar la seguridad.
3. **Optimización de Modelos:** Empaquetar los artefactos de modelos (serializados en la Phase Modeling) de modo que se carguen eficientemente en la memoria compartida del contenedor.

## 📐 II. Orquestación Local (Docker Compose)
1. **Definición de Redes:** Configurar redes internas para que el Frontend, Backend y Redis se comuniquen de forma aislada.
2. **Gestión de Volúmenes:** Mapear volúmenes para persistencia de logs y caché de modelos sin perder datos al reiniciar contenedores.

## 🚀 III. Seguridad de Contenedores
1. **User Privileges:** Asegurar que las aplicaciones no corran como `root` dentro de los contenedores.
2. **Scanning de Vulnerabilidades:** Auditar las imágenes en busca de librerías con fallos de seguridad conocidos antes de registrarlas.

---

> **Check de Certificación de Contenedores:**
> - [ ] ¿Las imágenes son lo suficientemente ligeras para un despliegue rápido?
> - [ ] ¿Se han separado correctamente las dependencias de desarrollo de las de producción?
> - [ ] ¿Se han expuesto solo los puertos estrictamente necesarios?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

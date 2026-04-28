---
name: data-security-governor
description: Protocolo para la implementación de capas de seguridad, cifrado y gobernanza de datos sensibles (PII) durante la ingesta.
user-invocable: false
agent: ai-data-engineer
allowed-tools: [Read, Write, Edit, Bash]
---

## 🏗️ I. Seguridad en Tránsito y Reposo
El agente debe garantizar que los datos estén protegidos en todo su viaje:
1. **Cifrado en Tránsito (TLS):** Asegurar que todas las conexiones a fuentes y destinos utilicen protocolos seguros.
2. **Cifrado en Reposo:** Configurar las claves de cifrado (KMS/Vault) para los archivos en el Storage (Bronze/Silver/Gold).
3. **Gestión de Secretos:** Nunca hardcodear credenciales; usar inyección de secretos en tiempo de ejecución.

## 📐 II. Tratamiento de Datos Sensibles (PII)
Identificar y proteger información de identificación personal:
1. **Detección Automática:** Escaneo de cabeceras para identificar campos como `email`, `dni`, `phone`.
2. **Enmascaramiento / Anonimización:** Aplicar hashing o máscaras (ej: `****@email.com`) en la frontera antes de que los datos sean legibles por otros agentes en capas superiores, si así lo dicta la política de privacidad.
3. **Audit Logging:** Registrar quién accedió a qué datos y cuándo.

## 🚀 III. Gobernanza y Acceso
1. **Implementación de RBAC:** Configurar permisos de acceso a nivel de prefijo o tabla según el rol del agente/usuario.
2. **Trazabilidad de Auditoría:** Mantener logs inmutables de todas las operaciones de lectura/escritura en el repositorio de datos.

---

> **Check de Certificación de Seguridad:**
> - [ ] ¿Se han anonimizado los datos PII según los requerimientos legales (GDPR/Local)?
> - [ ] ¿Las claves de acceso están almacenadas en un gestor de secretos seguro?
> - [ ] ¿Existe un registro de auditoría de acceso a los datos en crudo?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.

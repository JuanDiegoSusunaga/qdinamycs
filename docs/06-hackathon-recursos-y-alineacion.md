# 06 — Recursos del Hackathon y Alineación con QDinamycs

Este documento resume los recursos y patrocinadores tecnológicos del **Open Agent Hackathon 2026** de [GenAI Works](https://hackathon.genai.works/resources) y analiza cómo integrar sus herramientas (**Zetaris** y **Meterless**) con la arquitectura de la red interbancaria **QDinamycs**.

---

## 1. Estado de Acceso a `/resources`

* **Requisito de Plataforma:** La ruta `https://hackathon.genai.works/resources` (así como `/academy`) se encuentra protegida detrás de autenticación obligatoria.
* **Acceso:** Requiere registrarse o iniciar sesión gratuita con Google, LinkedIn o correo electrónico en [GenAI Works Sign In](https://hackathon.genai.works/sign-in?next=%2Fresources) para acceder a los enlaces de soporte, Discord de mentores, créditos de cómputo y tokens de API de los patrocinadores.

---

## 2. Tecnologías de Patrocinadores Clave

Para el track especial ***Wildcard [Tinkerer]: Bring Your Own Project*** (traer un proyecto previo propio como esta red interbancaria y extenderlo), el hackathon exige o premia explícitamente la integración de dos tecnologías patrocinadoras:

```
                      INTEGRACIÓN TECNOLÓGICA CON QDYNAMYCS
  
  ┌────────────────────────────────────────────────────────────────────────┐
  │                           AGENTE INTERBANCARIO                         │
  │                  (Orquestador de Pagos y Cumplimiento)                 │
  └───────────────────┬────────────────────────────────┬───────────────────┘
                      │                                │
                      ▼                                ▼
       ┌──────────────────────────────┐ ┌──────────────────────────────┐
       │     ZETARIS DATA PLATFORM    │ │       METERLESS BILLING      │
       │ (Malla de Datos Federada)    │ │ (Tarificación y Telemetría)  │
       ├──────────────────────────────┤ ├──────────────────────────────┤
       │ • Conecta fuentes bancarias  │ │ • Monitorea costo computa-   │
       │   fragmentadas sin moverlas. │ │   cional de las pruebas ZK.  │
       │ • Consultas AML y balances   │ │ • Micro-tarificación de gas  │
       │   entre sistemas legacy.     │ │   por transacción L2.        │
       └──────────────────────────────┘ └──────────────────────────────┘
                      │                                │
                      └────────────────┬───────────────┘
                                       ▼
                       [ RED INTERBANCARIA QDYNAMYCS ]
                       (Firmas ML-DSA + Pruebas STARK)
```

---

### A. Zetaris (Plataforma de Datos en Red / Data Mesh Federado)
* **¿Qué es?** Un motor de virtualización de datos que permite consultar múltiples fuentes heterogéneas *in-situ* (*query-in-place*) sin necesidad de mover ni duplicar los datos a un repositorio central.
* **Capacidades:**
  * Soporte de **Model Context Protocol (MCP)** para que agentes de IA se conecten directamente a bases de datos corporativas.
  * APIs REST autenticadas por Bearer Tokens y colecciones de Postman para gestionar esquemas virtuales (*Virtual Data Fabric*).
  * Control granular de políticas (OPA / Row & Column-level masking) para cumplimiento normativo.
* **Aplicación en QDinamycs:**
  * Los bancos comerciales tienen su información fragmentada en silos incompatibles: bases de datos relacionales del *Core Banking*, almacenes de mensajes ISO 20022 XML, listas de sanciones AML y libros de contabilidad históricos.
  * Un **Agente Auditor QDinamycs** puede usar Zetaris para investigar y federar consultas entre los silos bancarios internos sin violar el secreto bancario ni extraer datos fuera del perímetro del banco.

---

### B. Meterless (Medición de Uso, Telemetría y Facturación de Agentes)
* **¿Qué es?** Plataforma de tarificación y medición de consumo (*metering & usage-based billing*) diseñada específicamente para flujos de trabajo de agentes de IA y APIs asíncronas.
* **Capacidades:**
  * Registro y deduplicación de eventos estructurados en JSON con clave de idempotencia (`request_id`, `tokens`, `tool_calls`, `compute_time`).
  * Aplicación de límites de gasto en tiempo real (*circuit breakers* y *spend caps*).
  * Liquidación y conciliación automatizada de consumos.
* **Aplicación en QDinamycs:**
  * En una red interbancaria, cada banco participante genera costos de red al solicitar la generación y verificación de pruebas STARK o al ejecutar transacciones en la Capa Activa (L2).
  * **Meterless** permite implementar el sistema de tarificación de "gas bancario": mide con precisión milimétrica los recursos consumidos por cada banco emisor y permite liquidar las cuotas de uso de la red de forma transparente y auditable.

---

## 3. Estrategia de Participación en el Hackathon

Si se decide presentar **QDinamycs** al hackathon:
1. **Track Recomendado:** *Wildcard [Tinkerer]: Bring Your Own Project* o *The Agent That Can Explain Why*.
2. **Propuesta del Proyecto:**  
   *"QDinamycs: Red interbancaria post-cuántica y agente de liquidación resiliente. Conecta silos bancarios fragmentados mediante **Zetaris** para verificar transferencias ISO 20022, liquida atómicamente con firmas **ML-DSA** y pruebas **STARK (AIR/FRI)**, y tarifica el consumo de cómputo criptográfico mediante **Meterless**."*
3. **Plazo Crítico:** El registro en la plataforma cierra el **22 de octubre de 2026 a las 23:59 UTC**; la entrega final de la solución se realiza el **27 de octubre a las 23:45 UTC**.

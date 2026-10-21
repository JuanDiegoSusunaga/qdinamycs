# RESOURCES — Recursos, Herramientas y Referencias de QDinamycs

Este documento consolida todas las fuentes técnicas, enlaces de plataformas, estándares criptográficos y APIs relevantes para el desarrollo de la red interbancaria **QDinamycs**.

---

## 1. 🚀 Hackathon GenAI Works (Open Agent Hackathon 2026)

* **Portal del Evento:** [Open Agent Hackathon 2026](https://hackathon.genai.works/event/open-agent-hackathon-2026)
* **Página de Recursos (Requiere Login):** [GenAI Works Resources](https://hackathon.genai.works/resources)
* **Academia de Aprendizaje:** [GenAI Works Academy](https://hackathon.genai.works/academy)
* **Registro Directo:** [Sign-up Hackathon](https://hackathon.genai.works/sign-up?hackathon=019fd164-82b9-7382-a536-d9093c576ed2)
* **Comunidad Oficial Discord:** [Servidor de Discord GenAI Works](https://discord.gg/tfwSeqVWmR)

### Patrocinadores Tecnológicos Prioritarios
* **Zetaris (Networked Data Platform / Data Mesh):**
  * Portal: [zetaris.com](https://www.zetaris.com/)
  * Función en QDinamycs: Virtualización de datos bancarios heterogéneos (Core Banking, bases de datos AML, mensajes ISO 20022) mediante APIs REST y **Model Context Protocol (MCP)**.
* **Meterless (Agent Metering & Monetization):**
  * Función en QDinamycs: Tarificación de "gas interbancario", medición de consumo de inferencia y cómputo de pruebas STARK por nodo bancario.
* **Infraestructura Cloud e IA:**
  * Oracle Cloud, Google Cloud, AWS, IBM, NVIDIA, Groq (inferencia ultra-rápida LPU), BrightData (extracción de datos públicos) y Vectara (RAG semántico).

---

## 2. 🏛️ Estándares Financieros e Interbancarios (SWIFT & ISO 20022)

* **Estándar ISO 20022:**
  * Diccionario de mensajes financieros XML: [ISO 20022 Official Site](https://www.iso20022.org/)
  * `pacs.008`: *Financial Institutional Customer Credit Transfer* (pagos entre clientes).
  * `pacs.009`: *Financial Institution Credit Transfer* (liquidación directa entre bancos).
  * `camt.053`: *Bank-to-Customer Statement* (estados de cuenta y conciliación).
* **Guías de Migración SWIFT CBPR+:**
  * [Cross-Border Payments and Reporting Plus (CBPR+)](https://www.swift.com/standards/iso-20022/cbpr)
* **Seguridad y Regulación:**
  * **SWIFT CSP:** *Customer Security Programme* (Marco de controles de seguridad obligatorios).
  * **GAFI / FATF Travel Rule:** Recomendación 16 para transmisión de información de ordenante y beneficiario en transferencias electrónicas.

---

## 3. 🔐 Criptografía Post-Cuántica (PQC) y Fuentes de Entropía

* **Estándares Oficiales NIST PQC (Publicados en Agosto 2024):**
  * [NIST FIPS 204 — ML-DSA](https://csrc.nist.gov/pubs/fips/204/final): *Module-Lattice-Based Digital Signature Standard* (Basado en CRYSTALS-Dilithium). Núcleo de firma de QDinamycs.
  * [NIST FIPS 203 — ML-KEM](https://csrc.nist.gov/pubs/fips/203/final): *Module-Lattice-Based Key-Encapsulation Mechanism* (Basado en CRYSTALS-Kyber). Cifrado de canales privados P2P.
  * [NIST FIPS 205 — SLH-DSA](https://csrc.nist.gov/pubs/fips/205/final): *Stateless Hash-Based Digital Signature Standard* (SPHINCS+).
* **Calidad y Monitoreo de Entropía:**
  * [NIST SP 800-90B](https://csrc.nist.gov/pubs/sp/800/90/b/final): *Recommendation for the Entropy Sources Used for Random Bit Generation*.
  * Pruebas continuas de salud: **RCT** (*Repetition Count Test*) y **APT** (*Adaptive Proportion Test*) con ventanas $W=512$ y corte $C=16$.

---

## 4. 📐 Verificación Formal y Pruebas de Validez (Herencia NEXUS)

* **Repositorio Local de la Tesis:** `C:\Users\juand\Desktop\Tesis`
* **Implementación Rust de Referencia (`nexus/`):**
  * `nexus-crypto`: Implementación de ML-DSA con backend `fips204` y derivación determinista contextual.
  * `nexus-zk`: Motor STARK (AIR/FRI) sobre el campo primo Goldilocks ($p = 2^{64} - 2^{32} + 1$).
  * `nexus-sentinel`: Monitor de min-entropía en tiempo real y pruebas RCT/APT.
  * `nexus-active`: Secuenciador y cadena activa L2.
  * `nexus-anchor`: Contrato y anclaje soberano L1.
* **Verificación Formal en Lean 4 (`formalizacion/`):**
  * Entorno: Lean 4.35.0 con Mathlib.
  * Alcance: 2.709 declaraciones auditadas sin `sorryAx` demostrando la cota conservadora de solidez en ROM clásico finito para el verificador de bytes AIR/FRI.

---

## 5. 📦 Repositorio del Proyecto

* **GitHub:** [JuanDiegoSusunaga/qdinamycs](https://github.com/JuanDiegoSusunaga/qdinamycs)
* **Visibilidad:** Privado
* **Rama Principal:** `main`

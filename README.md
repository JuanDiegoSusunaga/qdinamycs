# QDinamycs — Red Interbancaria Resiliente Post-Cuántica

> **Visión:** Modernizar la infraestructura financiera global de pagos interbancarios (superando las limitaciones de SWIFT) mediante una arquitectura blockchain dual permisionada, firmas post-cuánticas (ML-DSA), pruebas de validez STARK (AIR/FRI) y gestión activa del ciclo de vida de claves.

---

## 📌 Contexto y Motivación

El sistema financiero internacional depende críticamente de **SWIFT** y de la red de **banca corresponsal**. Sin embargo, este modelo enfrenta hoy dos crisis simultáneas:
1. **Crisis de Eficiencia y Coste:** Mensajes excesivamente pesados (migración a ISO 20022 XML con sobrecostos de datos de hasta $5.000\%$), billones de dólares atrapados en cuentas *Nostro/Vostro* para prefondeo de liquidez y conciliaciones manuales que tardan días ($T+2$ o $T+3$).
2. **Crisis Cuántica Inminente:** Toda la seguridad de SWIFT (RSA y curvas elípticas ECDSA) colapsará bajo el **algoritmo de Shor**. Además, la amenaza **HNDL (*Harvest Now, Decrypt Later*)** ya está activa contra el secreto bancario internacional. La migración ingenua a firmas post-cuánticas generaría una sobrecarga de datos inmanejable para las redes actuales.

**QDinamycs** aplica los fundamentos matemáticos y computacionales desarrollados en la tesis **NEXUS** (firmas ML-DSA FIPS 204, monitor de entropía NIST SP 800-90B y compresión de transacciones mediante STARKs sobre campos Goldilocks) para construir una red interbancaria de alta velocidad, privada, formalmente verificada e inmune a computadores cuánticos.

---

## 🗂️ Estructura de Documentación

Todo el conocimiento, análisis y especificaciones técnicas se organizan en los siguientes documentos:

1. [**01. Análisis Profundo de SWIFT y su Sobrecosto**](docs/01-analisis-swift.md):
   * Cómo funciona realmente SWIFT (*messaging vs. settlement*).
   * Red SWIFTNet, interfaces Alliance y flujo de banca corresponsal (*Nostro/Vostro*).
   * El triple sobrepeso: datos (MT a ISO 20022), liquidez atrapada e infraestructura legada.

2. [**02. La Amenaza Cuántica en la Banca Global**](docs/02-amenaza-cuantica-banca.md):
   * Impacto de Shor sobre RSA y ECC.
   * La amenaza presente *Harvest Now, Decrypt Later* (HNDL).
   * La "Paradoja Post-Cuántica": por qué añadir firmas PQC planas a SWIFT colapsaría el sistema.

3. [**03. Arquitectura de la Red QDinamycs**](docs/03-arquitectura-qdinamycs.md):
   * Arquitectura multi-nivel: L1 (Anclaje Soberano) + L2 (Consorcio Activo) + L3 (Canales Privados P2P).
   * Consenso privado y ligero (QBFT / PoA): finalidad determinista en $<1$ segundo sin minería.
   * Privacidad bancaria absoluta: separación de datos de negocio y pruebas STARK de validez.
   * Acceso selectivo y cumplimiento normativo (AML/CFT) mediante llaves de auditoría (*viewing keys*).
   * Escalabilidad masiva ($+5.000$ TPS amortizados).

4. [**04. Seguridad Activa y Gestión de Información Comprometida**](docs/04-seguridad-y-resiliencia.md):
   * Monitor de entropía en tiempo real (*Sentinel*, NIST SP 800-90B: RCT y APT).
   * Secrecía hacia adelante (*Forward Secrecy*) mediante rotación de claves por épocas.
   * Protocolo de cuarentena y aislamiento on-chain ante intrusiones o nodos comprometidos.
   * Pista de auditoría forense inmutable.

5. [**05. Caso de Negocio e Inversión para Bancos**](docs/05-business-case-bancos.md):
   * Comparativa de CAPEX y OPEX: SWIFT Alliance vs. Nodos Ligeros QDinamycs.
   * Liberación de capital atrapado en corresponsalía.
   * Integración con sistemas *Core Banking* tradicionales vía API/gRPC e ISO 20022.

---

## 🔬 Fundamentación Técnica (Herencia de la Tesis NEXUS)

* **Criptografía Post-Cuántica:** ML-DSA (CRYSTALS-Dilithium) en modo determinista y aleatorizado (*hedged*), conforme a NIST FIPS 204.
* **Pruebas de Validez STARK (AIR/FRI):** Demostración formal de solidez bajo el modelo ROM clásico finito comprobada en **Lean 4** (2.709 declaraciones auditadas sin axiomas no estándar).
* **Gestión de Entropía:** Monitoreo continuo de fuentes aleatorias con test de conteo de repeticiones (RCT) y test de proporción adaptativa (APT) con ventanas $W=512$ y corte $C=16$.

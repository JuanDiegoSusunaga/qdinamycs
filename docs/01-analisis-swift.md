# 01 — Análisis Profundo de SWIFT y su Sobrecosto

Este documento analiza la arquitectura técnica, el modelo operativo y las causas del elevado coste ("peso") de la red **SWIFT** (*Society for Worldwide Interbank Financial Telecommunications*).

---

## 1. Desmitificando SWIFT: ¿Qué hace y qué no hace?

Existe una concepción errónea generalizada de que SWIFT transfiere dinero. **SWIFT no liquida ni almacena fondos.** 

SWIFT es una **red cerrada y cooperativa de mensajería financiera segura** basada en una arquitectura de almacenamiento y reenvío (*store-and-forward*). Su función es transmitir instrucciones estandarizadas entre instituciones financieras para que estas posteriormente coordinen el movimiento contable de los fondos.

```
                  EL FLUJO REAL DE UN PAGO VÍA SWIFT
                  
  [ Banco Emisor ] ────────(Mensaje SWIFT)────────► [ Banco Receptor ]
         │                                                  ▲
         │ (Instrucción de débito/crédito)                  │
         ▼                                                  │
  [ Cuenta Nostro ] ──(Liquidación Contable Real)──► [ Cuenta Vostro ]
   (En Banco Corresponsal)                            (Fondos Pre-fondeados)
```

---

## 2. Componentes de la Arquitectura SWIFT

1. **SWIFTNet:** La red de transporte de capa IP, operada sobre enlaces MPLS dedicados y cifrados provistos por operadores de telecomunicaciones certificados.
2. **Servicios de Mensajería:**
   * **SWIFTNet FIN:** El protocolo tradicional para mensajes estructurados en formato MT.
   * **SWIFTNet FINplus / InterAct:** La plataforma para el transporte de mensajes interactivos basados en el estándar ISO 20022 (formato MX).
   * **SWIFTNet FileAct:** Servicio de transferencia masiva de archivos para procesamiento de lotes contables o reportes de tesorería.
3. **Plataforma de Interfaz (CPE - Customer Premises Equipment):**
   * **Alliance Access / Alliance Gateway:** Software propietario pesado que cada banco debe desplegar en su propia infraestructura o en una nube privada calificada.
   * **Hardware Security Modules (HSMs):** Dispositivos físicos obligatorios (con certificación FIPS 140-2 Nivel 3) donde se almacenan las llaves privadas de autenticación de SWIFT y se firman los mensajes.
4. **Relaciones Bilaterales (RMA - Relationship Management Application):**
   * Antes de enviarse mensajes, dos bancos deben establecer una autorización mutua en la red mediante certificados cruzados.

---

## 3. ¿Por qué "pesa tanto" SWIFT?

El sobrecosto de SWIFT no se limita al tamaño de los datos en red, sino que abarca tres dimensiones críticas:

```
                            EL TRIPLE SOBREPESO DE SWIFT
                                         │
        ┌────────────────────────────────┼────────────────────────────────┐
        ▼                                ▼                                ▼
  [ Peso de Datos ]            [ Peso de Liquidez ]             [ Peso Criptográfico ]
• Migración a ISO 20022       • Cuentas Nostro / Vostro        • Dependencia de RSA/ECC
  (XML inflado x10 - x50)       con capital congelado          • Costo de HSMs físicos
• Tags XML redundantes        • Cadenas de intermediarios      • Imposibilidad de soportar
• Validación pesada XSD         (T+2, comisiones acumuladas)     firmas PQC sin colapsar
```

### A. Sobrepeso de Datos y Formatos (Data Bloat)

* **El legado MT (Message Type):**
  * Diseñado en los años 70 como texto plano delimitado por etiquetas numéricas (`:20:`, `:32A:`, `:50K:`).
  * Límite estricto de **10.000 caracteres** por mensaje.
  * *Problema:* Datos extremadamente ambiguos que producían hasta un **15% de falsos positivos** en los motores automáticos de sanciones y antilavado (AML), deteniendo operaciones durante días para revisión humana manual.
* **El estándar moderno ISO 20022 (MX):**
  * Estructurado íntegramente en XML (`pacs.008` para transferencias de clientes, `pacs.009` para transferencias entre bancos).
  * Incluye una cabecera de negocio (*Business Application Header* - BAH), trazabilidad completa de intermediarios y detalles exhaustivos de remesa.
  * *Sobrecosto técnico:* Un mensaje que en formato MT pesaba **~1 KB**, en ISO 20022 pesa entre **10 KB y 50 KB** (un aumento de **$1.000\% \text{ a } 5.000\%$** en bytes).
  * Multiplicado por más de **45 millones de mensajes diarios**, esto satura el almacenamiento y procesamiento de los *Cores Bancarios*.

### B. Sobrepeso de Liquidez y Banca Corresponsal (Liquidity Bloat)

Como SWIFT no mueve fondos en tiempo real, la liquidación transfronteriza depende de la **banca corresponsal**:
* **Cuentas Nostro/Vostro:** Para que un banco en un país A envíe dinero a un banco en un país B, debe mantener saldos líquidos pre-fondeados en bancos internacionales.
* **Capital Atrapado:** Se calcula que a nivel global existen **billones de dólares inmovilizados** en cuentas de corresponsalía únicamente como "colchón de liquidez", lo cual representa un coste de oportunidad gigantesco para el sector financiero.
* **Cadenas de Saltos (*Multi-hop Routing*):** Si dos bancos no tienen relación bilateral directa, el mensaje SWIFT salta por 2 o 3 bancos intermediarios. Cada salto añade:
  * Comisiones deducidas del monto original.
  * Retrasos de liquidación de 24 a 72 horas ($T+2$ o $T+3$).
  * Riesgo crediticio y de contraparte.

### C. Sobrepeso Criptográfico y Operativo

* **PKI Centralizada y HSMs:** Cada banco depende de certificados digitales emitidos por la Autoridad Certificadora de SWIFT (basados en RSA 2048/4096 o ECC).
* **Costes de Cumplimiento (CSP):** El *Customer Security Programme* de SWIFT impone auditorías anuales obligatorias y estrictos controles perimetrales debido a ataques históricos de inyección de mensajes en interfaces locales (ej. el hackeo al Banco Central de Bangladesh en 2016 por $81 millones USD).
* **Incompatibilidad con Criptografía Post-Cuántica Plana:** Si SWIFT intentara añadir firmas post-cuánticas (como ML-DSA / Dilithium) directamente a los mensajes XML, el tamaño por mensaje superaría fácilmente los 60 KB, haciendo inviable su red de transporte tradicional sin una reestructuración completa.

# 03 — Arquitectura de la Red QDinamycs

Este documento detalla la arquitectura técnica por niveles (*multi-tier*), el protocolo de consenso ligero, el modelo de privacidad basado en pruebas de validez y los mecanismos de escalabilidad y auditoría de la red interbancaria **QDinamycs**.

---

## 1. Arquitectura en Tres Niveles (Multi-Tier)

La red adopta un modelo jerárquico diseñado para aislar las operaciones financieras masivas de la capa de liquidación soberana:

```
                            ARQUITECTURA POR NIVELES
  
  ┌────────────────────────────────────────────────────────────────────────┐
  │  NIVEL 1: CAPA SOBERANA / ANCLAJE (L1 - Anchor Layer)                 │
  │  Operadores: Bancos Centrales (FED, BCE, BanRep) y Cámaras de Comp.    │
  │  • Verificador matemático formalizado en Lean 4 (STARK AIR/FRI).       │
  │  • Liquidación neta atómica multilateral (DvP / PvP).                  │
  │  • No almacena datos bancarios ni firmas individuales.                 │
  └───────────────────────────────────▲────────────────────────────────────┘
                                      │  1 Prueba STARK (~80-120 KB)
                                      │  + Compromiso de Estado (Merkle Root)
  ┌───────────────────────────────────┴────────────────────────────────────┐
  │  NIVEL 2: CONSORCIO ACTIVO (L2 - Active Layer)                         │
  │  Operadores: Nodos validadores permisionados de Bancos Comerciales     │
  │  • Consenso privado ligero (QBFT / PoA) con finalidad < 1 segundo.     │
  │  • Ejecución y verificación masiva de firmas ML-DSA (FIPS 204).       │
  │  • Generación de pruebas STARK de transición de estado.                │
  └───────────────────────────────────▲────────────────────────────────────┘
                                      │  Transacciones de pago firmadas
                                      │  + Compromiso de mensaje (Hash)
  ┌───────────────────────────────────┴────────────────────────────────────┐
  │  NIVEL 3: PRIVACIDAD Y DATOS (Canales Bilaterales P2P)                 │
  │  Operadores: Banco Emisor ◄─────── Payload Cifrado ──────► Banco Receptor │
  │  • Mensaje de negocio completo (ISO 20022 `pacs.008` enriquecido).     │
  │  • Cifrado post-cuántico extremo a extremo con ML-KEM (FIPS 203).      │
  │  • Los datos comerciales nunca tocan la blockchain pública de la L2.   │
  └────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Consenso Privado y Ligero (Sin Minería ni Desperdicio)

A diferencia de las redes públicas abiertas, la banca internacional opera en un entorno regulado y permisionado donde cada participante posee una identidad legal verificada.

* **Algoritmo de Consenso:** **QBFT (Quorum Byzantine Fault Tolerance)** adaptado con atestaciones post-cuánticas o **IBFT 2.0**.
* **Propiedades:**
  * **Finalidad Inmediata y Determinista:** Cada bloque confirmado es 100% definitivo en **menos de 1 segundo**. No existen bifurcaciones (*forks*) ni necesidad de esperar múltiples confirmaciones.
  * **Resistencia a Fallos Bizantinos:** Tolera hasta $\lfloor (N-1)/3 \rfloor$ nodos maliciosos o caídos en una red de $N$ validadores.
  * **Eficiencia Energética y Computacional:** Los nodos gastan sus ciclos de CPU en verificar criptografía y generar pruebas STARK, no en minería inútil.

---

## 3. Privacidad Bancaria Absoluta y Pruebas STARK

El mayor obstáculo para adoptar tecnología DLT en la banca tradicional es la confidencialidad: **un banco competidor jamás debe ver los saldos, los clientes ni las transacciones de otra entidad.**

### Separación entre Validez y Datos de Negocio

QDinamycs resuelve esto desacoplando la **demostración de validez matemática** de los **datos sensibles del negocio**:

```
                       SEPARACIÓN DE FLUJOS EN QDYNAMYCS
  
   [ Datos de Negocio Sensibles ]               [ Prueba Criptográfica de Validez ]
  • Nombres de remitente y beneficiario        • El emisor tiene fondos suficientes.
  • Números de cuenta y facturas               • La firma ML-DSA es matemáticamente auténtica.
  • Montos exactos de la operación             • El balance neto global cuadra al centavo.
                 │                                              │
                 ▼                                              ▼
   Viajan CIFRADOS punto a punto                  Viaja a la L2 y L1 como una
   solo entre Banco Emisor y Receptor             PRUEBA STARK en campo Goldilocks
   (Nadie más en la red puede leerlos)            (Verificable por todos sin revelar datos)
```

### El Rol de los STARKs (AIR/FRI sobre Campo Goldilocks)
Heredado del crate `nexus-zk` de la tesis:
* Se compila la transición contable en una representación intermedia algebraica (**AIR** - *Algebraic Intermediate Representation*).
* Se evalúa la proximidad polinomial mediante el protocolo **FRI** (*Fast Reed-Solomon Interactive Oracle Proof of Proximity*).
* Toda la red L2/L1 puede verificar matemáticamente que un lote de 10.000 transacciones es legítimo sin que los nodos validadores tengan acceso al contenido privado de ninguna de ellas.

---

## 4. Acceso Selectivo y Cumplimiento Regulatorio (AML / CFT)

Para operar dentro del marco de Basilea III, GAFI/FATF y las normativas bancarias locales:

* **Llaves de Auditoría (*Viewing Keys*):**  
  Al firmar una transacción, el banco emisor cifra los metadatos de cumplimiento regulatorio (identidades del ordenante y beneficiario conforme a la *Travel Rule* del GAFI) utilizando la clave pública post-cuántica del **Banco Central o del Supervisor Financiero**.
* **Auditoría en Tiempo Real:**  
  El regulador tiene acceso para descifrar e inspeccionar cualquier transacción sospechosa en tiempo real, sin depender de reportes diferidos ni interrumpir el flujo del sistema.

---

## 5. Escalabilidad y Amortización del Sobrecosto

| Métrica | Red SWIFT Tradicional | Migración PQC Plana | Red QDinamycs (L2 + STARKs) |
| :--- | :---: | :---: | :---: |
| **Capacidad de Procesamiento** | 600 – 1.000 TPS | < 100 TPS (Saturación) | **+5.000 TPS** |
| **Tiempo de Liquidación** | $T+1$ a $T+3$ días | $T+1$ a $T+3$ días | **Sub-segundo en L2 / Minutos en L1** |
| **Costo de Almacenamiento L1** | Centralizado (TB/día) | Crítico (>100 GB/hora) | **Sublineal ($O(\text{diff})$)** |
| **Resistencia Cuántica** | Nula (Roto por Shor) | Sí (pero ineficiente) | **Totalmente Inmune** |

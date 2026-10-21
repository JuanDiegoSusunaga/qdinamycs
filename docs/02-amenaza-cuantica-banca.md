# 02 — La Amenaza Cuántica en la Banca Global

Este documento detalla el impacto del advenimiento de la computación cuántica sobre la infraestructura financiera internacional, analizando los modelos de amenaza, la vulnerabilidad de los esquemas criptográficos vigentes y la paradoja del sobrecosto en la migración a criptografía post-cuántica (PQC).

---

## 1. El Algoritmo de Shor y la Ruptura de los Fundamentos Bancarios

El sistema bancario moderno opera sobre dos familias de criptografía de clave pública:
* **RSA:** Basado en la dureza de la factorización de enteros grandes.
* **ECC (Criptografía de Curvas Elípticas):** Basado en el problema del logaritmo discreto sobre grupos de curvas elípticas (ej. secp256k1 en finanzas descentralizadas, curvas NIST P-256 en banca tradicional).

En 1994, Peter Shor formuló un algoritmo cuántico que resuelve **ambos problemas en tiempo polinómico** ($\mathcal{O}((\log N)^3)$). Esto significa que un computador cuántico con suficiente tolerancia a fallos (denominado **CRQC** - *Cryptanalytically Relevant Quantum Computer*) romperá de forma trivial:
* Todos los certificados digitales de las pasarelas interbancarias.
* Los mecanismos de intercambio de llaves Diffie-Hellman y RSA.
* Todas las firmas digitales (ECDSA, RSA-PSS) utilizadas para autorizar pagos y transferencias SWIFT.

---

## 2. El Modelo de Amenaza en Finanzas: HNDL vs. Autenticación

Es crucial distinguir las dos vertientes de la amenaza cuántica en el contexto financiero:

```
                          MODELO DE AMENAZA CUÁNTICA
                                      │
         ┌────────────────────────────┴────────────────────────────┐
         ▼                                                         ▼
   [ A. Confidencialidad: HNDL ]                             [ B. Autenticación: Firmas ]
• Amenaza PRESENTE y activa.                              • Amenaza FUTURA pero CATASTRÓFICA.
• Interceptan tráfico interbancario hoy;                 • Ruptura de la firma en tiempo real.
  lo descifran mañana cuando tengan un CRQC.             • Falsificación indetectable de órdenes
• Afecta: Secretos bancarios y transferencias.             de pago interbancarias.
```

### A. Harvest Now, Decrypt Later (HNDL)
Los actores estatales y adversarios avanzados ya están interceptando y almacenando terabytes de tráfico financiero cifrado con TLS/RSA clásico. Aunque hoy no pueden descifrarlo, lo harán en cuanto el CRQC sea una realidad. Esto viola los plazos legales de secreto bancario y confidencialidad financiera (que a menudo exigen protección durante 20, 30 o 50 años).

### B. Amenaza a la Autenticación (El foco de QDinamycs)
Si un atacante dispone de un CRQC, puede deducir la llave privada de cualquier banco a partir de su clave pública expuesta en los certificados SWIFT o transacciones previas. Con esto, el atacante puede **firmar órdenes de pago falsas** que cualquier validador tradicional aceptará como legítimas.

---

## 3. La "Paradoja Post-Cuántica" en el Sector Financiero

Para contrarrestar la amenaza cuántica, el **NIST** (Instituto Nacional de Estándares y Tecnología de EE. UU.) culminó en agosto de 2024 la estandarización de los algoritmos post-cuánticos:
* **FIPS 203:** ML-KEM (Mecanismo de encapsulado de claves basado en retículos).
* **FIPS 204:** **ML-DSA** (Esquema de firma digital basado en retículos, derivado de CRYSTALS-Dilithium).
* **FIPS 205:** SLH-DSA (Firmas basadas en funciones hash sin estado, derivado de SPHINCS+).

### El Dilema del Tamaño

La seguridad basada en la dureza de los retículos euclídeos (**Module-LWE / Module-SIS**) no es gratuita; exige vectores polinomiales mucho mayores que los puntos escalares de las curvas elípticas.

| Esquema de Firma | Nivel de Seguridad | Tamaño Clave Pública | Tamaño de la Firma | Incremento vs. ECDSA |
| :--- | :---: | :---: | :---: | :---: |
| **ECDSA (P-256 / secp256k1)** | Clásico 128 bits | 64 Bytes | **64 Bytes** | Referencia ($1\times$) |
| **RSA-2048** | Clásico 112 bits | 256 Bytes | **256 Bytes** | $4\times$ |
| **ML-DSA-44 (Nivel II NIST)** | Post-cuántico 128 bits | 1.312 Bytes | **2.420 Bytes** | **$37\times$** |
| **ML-DSA-65 (Nivel III NIST)** | Post-cuántico 192 bits | 1.952 Bytes | **3.309 Bytes** | **$\sim 51\times$** |
| **ML-DSA-87 (Nivel V NIST)** | Post-cuántico 256 bits | 2.592 Bytes | **4.627 Bytes** | **$72\times$** |

### Por qué una migración ingenua colapsaría la red actual

Si una red interbancaria como SWIFT simplemente "reemplazara" las firmas ECDSA por firmas ML-DSA dentro de sus mensajes ISO 20022 XML:
1. **Multiplicación del Ancho de Banda:** Cada firma requiere $\sim 3,3\text{ KB}$. Si un mensaje bancario requiere múltiples firmas de la cadena de corresponsales y custodios, la carga de datos por mensaje se dispararía a más de 50–100 KB.
2. **Saturación de Almacenamiento y Cómputo:** Los bancos centrales y comerciales tendrían que procesar y archivar gigabytes por segundo de firmas redundantes.
3. **El Trilema Cuántico:** Seguridad post-cuántica directa en una red centralizada o de cadena única implica una degradación crítica del rendimiento ($20\times$ menor TPS bajo anchos de banda constantes).

---

## 4. La Solución de QDinamycs

La respuesta teórica demostrada en la tesis **NEXUS** consiste en **no transmitir ni almacenar individualmente las firmas ML-DSA en la capa central de liquidación**. 

En su lugar:
* Las firmas completas ML-DSA se verifican de forma masiva y ultrarrápida en una **Capa Activa (L2)**.
* Se genera una prueba sucinta **STARK (AIR/FRI)** que comprime la validez de miles de firmas post-cuánticas en un único comprobante criptográfico.
* La capa de liquidación **(L1)** solo valida la prueba STARK, haciendo que el coste por transacción tienda únicamente a la diferencia de estado ($O(\text{sublineal})$).

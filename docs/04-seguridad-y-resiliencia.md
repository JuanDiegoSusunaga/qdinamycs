# 04 — Seguridad Activa y Gestión de Información Comprometida

Este documento detalla la autodefensa criptográfica, la monitorización continua de entropía conforme a estándares del NIST, la secrecía hacia adelante y los protocolos de respuesta y contención ante incidentes donde un nodo bancario o sus llaves privadas se vean comprometidos.

---

## 1. El Riesgo Histórico en la Banca: Ataques a la Infraestructura Local

El protocolo SWIFT en sí rara vez ha sido vulnerado en tránsito; los mayores ataques de la historia financiera (como el ciberrobo al Banco Central de Bangladesh en 2016 por $81 millones USD) ocurrieron porque los atacantes comprometieron el **entorno local del banco** (credenciales de operadores, malware en servidores locales o alteración de software de interfaz).

En un sistema tradicional con claves estáticas o certificados anuales:
* Si un atacante extrae una llave privada de un banco, puede firmar transferencias fraudulentas durante horas o días antes de ser detectado.
* Si el generador de números aleatorios (RNG) de un banco se degrada o es saboteado, las llaves generadas se vuelven predecibles.

---

## 2. Monitor Activo de Entropía (*Sentinel* / NIST SP 800-90B)

Heredado del módulo `nexus-sentinel` de la tesis, cada nodo bancario integra un monitor en tiempo real del flujo de entropía física:

```
                    MONITOREO DE ENTROPÍA CONTINUO
  
   [ Fuente de Entropía ] ──► [ Pruebas de Salud NIST SP 800-90B ]
                                • RCT (Repetition Count Test)
                                • APT (Adaptive Proportion Test)
                                     │
                 ┌───────────────────┴───────────────────┐
                 ▼                                       ▼
        [ Entropía Nominal ]                   [ Fallo / Sabotaje ]
                 │                                       │
                 ▼                                       ▼
    Permite Derivación de Clave                BLOQUEO INMEDIATO DEL NODO
    y Firma de Transacciones                   (Impide firmar con claves débiles)
```

### Pruebas Estadísticas Continuas
* **RCT (Repetition Count Test):** Detecta fallos catastróficos si el generador se congela y produce el mismo valor repetidamente.
* **APT (Adaptive Proportion Test):** Con una ventana $W = 512$ muestras y un corte crítico $C = 16$, detecta desviaciones estadísticas sutiles que indicarían manipulación de hardware o degradación térmica.
* **Bloqueo Automático Preventivo:** Si el monitor detecta una anomalía de entropía, **suspende automáticamente el motor de firma** antes de que el nodo pueda emitir una clave comprometida.

---

## 3. Secrecía hacia Adelante Condicionada (*Forward Secrecy*)

Para evitar que una intrusión presente comprometa el pasado histórico del banco:

* **Claves por Épocas:** Las llaves operativas de firma no son estáticas. Se derivan de forma determinista y contextual para ventanas de tiempo discretas (ej. cada 60 minutos o cada turno operativo):
  $$\text{sk}_{t} = \text{KDF}(\text{Seed}, \text{Contexto} \parallel \text{Época}_t)$$
* **Higiene de Memoria:** Al finalizar una época, el material criptográfico efímero de la llave previa se sobrescribe en memoria de forma segura.
* **Garantía Matemática:** Si un adversario compromete la clave de la época actual $\text{sk}_t$, **le resulta matemáticamente imposible falsificar transacciones de épocas pasadas ($\text{sk}_{t-1}, \text{sk}_{t-2}$)** o alterar el historial inmutable ya consolidado.

---

## 4. Protocolo ante Información Comprometida (Incidente de Seguridad)

Si un banco miembro de QDinamycs detecta una intrusión o la fuga de credenciales en sus instalaciones:

```
                      PROTOCOLO DE CUARENTENA ON-CHAIN
  
  [ 1. Detección de Fuga ] ──► [ 2. Alerta Criptográfica al Consorcio ]
                                          │
                                          ▼
                               [ 3. Revocación de Época ]
                                • La L2 invalida la clave de la época actual.
                                • Transacciones pendientes no confirmadas se congelan.
                                          │
                                          ▼
                               [ 4. Aislamiento On-Chain ]
                                • El banco entra en "Modo Solo-Lectura".
                                • La red interbancaria global SIGUE OPERANDO.
                                          │
                                          ▼
                               [ 5. Auditoría Forense Inmutable ]
                                • Análisis de logs hash-chained para deslindar
                                  firmas legítimas de intentos fraudulentos.
```

1. **Aislamiento sin Caída del Sistema:** La red revoca el certificado de época del banco comprometido en el siguiente bloque de la L2. El banco afectado queda en cuarentena, pero el resto del sistema bancario mundial continúa operando sin interrupción.
2. **Pista de Auditoría Forense Inmutable:**  
   Cada transacción confirmada en QDinamycs está anclada a una raíz de Merkle y a una prueba STARK formalizada. Ante una disputa legal o fraude:
   * Se puede demostrar matemáticamente ante un tribunal qué transacciones ocurrieron antes del compromiso y cuáles intentó inyectar el atacante.
   * Ningún operador bancario interno corrupto puede borrar o alterar los registros contables históricos para encubrir un desfalco.

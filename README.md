# RoutePay Protocol

**Smart Trade & Logistics Escrow con Tangem NFC** — Custodia criptográfica de pagos de fletes para el corredor comercial **Arica – Tambo Quemado – Santa Cruz / Cochabamba**.

Desarrollado para la **Buildathon de ETH Bolivia 2026** (Cochabamba, 11–13 de septiembre de 2026).

---

## 🔗 Enlaces Oficiales

| Recurso | Enlace / Detalle |
|---|---|
| **GitHub (este repositorio)** | [https://github.com/RonaldGaymer2002/RoutePay](https://github.com/RonaldGaymer2002/RoutePay) |
| **Demo en Vivo (dApp)** | [https://frontend-mu-bay-zvc6ih2zcs.vercel.app](https://frontend-mu-bay-zvc6ih2zcs.vercel.app) |
| **Red** | Avalanche Fuji Testnet — Chain ID `43113` |
| **Contrato `TradeEscrow`** | [`0x33cA337680366d337931a6f22226214d09594011`](https://testnet.snowtrace.io/address/0x33cA337680366d337931a6f22226214d09594011#code) — **Código fuente verificado ✅** |
| **`ERC2771Forwarder` (Gasless)** | [`0x33E5Adf857F02C5e56174c99b610C86e04610a6D`](https://testnet.snowtrace.io/address/0x33E5Adf857F02C5e56174c99b610C86e04610a6D#code) — **Código fuente verificado ✅** |
| **USDC (Circle, testnet)** | [`0x5425890298aed601595a70AB815c96711a31Bc65`](https://testnet.snowtrace.io/address/0x5425890298aed601595a70AB815c96711a31Bc65) |

> Ambos contratos tienen su código Solidity real verificado y público en SnowTrace (pestaña `#code`) y cuentan con coincidencia exacta de bytecode verificada en [Sourcify](https://repo.sourcify.dev/contracts/full_match/43113/0x33cA337680366d337931a6f22226214d09594011/).

---

## 🎯 ¿Qué es RoutePay?

En el transporte pesado internacional, los transportistas cobran sus fletes entre **30 y 60 días después de la entrega** y enfrentan estafas constantes con comprobantes bancarios falsos. Al mismo tiempo, los importadores sufren por la falta de divisas (dólares físicos en Bolivia) y pagan comisiones bancarias abusivas del **8% al 12%** que tardan semanas en liquidarse.

`TradeEscrow.sol` custodia el pago del flete en stablecoins (USDC) y lo **libera de forma instantánea e irrevocable** en el segundo exacto en que el encargado del almacén de destino apoya su tarjeta física **Tangem NFC** contra el celular del transportista: **sin bancos, sin intermediarios y con prueba de presencia física**.

El protocolo cobra una comisión de liquidación transparente del **0.5%** en `settleWithTangemTap`, deducida automáticamente al momento de la liberación.

---

## ⚙️ ¿Cómo Funciona?

```
[Importador] --(1. Fondeo Gasless USDC)--> [TradeEscrow.sol]
                                                 |
[Transportista/Aduana] --(2. Inicia Tránsito)----+
                                                 |
[Receptor en Almacén] --(3. Tap Tangem NFC)-----+--> [Liquidación Instantánea (USDC)]
                                                        - 99.5% al Transportista
                                                        - 0.5% a Tesorería
```

0. **Selección de Rol e Inicio:** La dApp presenta un selector de roles (Importador / Transportista / Aduana) que adapta la vista al flujo relevante de cada actor.
1. **El Importador fondea la orden:** Deposita USDC en `TradeEscrow` sin pagar gas mediante meta-transacciones ERC-2771 y permisos ERC-2612.
2. **Inicio del tránsito:** Confirmado por el transportista o mediante un oráculo aduanero autorizado (`customsOracle`).
3. **Liberación por Tap Tangem NFC:** El receptor firma criptográficamente mediante el chip seguro de su tarjeta física Tangem (firma EIP-712); el contrato valida la firma on-chain y transfiere los fondos al transportista en un segundo.
4. **Resolución de disputas:** Mecanismo integrado con árbitro designado si el receptor no confirma la entrega dentro del plazo convenido.

---

## 🛠️ Stack Tecnológico

- **Smart Contracts:** Solidity 0.8.24 + Foundry, desplegados y verificados en **Avalanche Fuji Testnet**.
- **Gasless (Cero Gas):** Meta-transacciones ERC-2771 + ERC-2612 Permit — el importador y el chofer firman off-chain; un relayer asume el gas mientras el contrato preserva la identidad criptográfica del firmante real.
- **Confirmación Aduanera:** Rol `customsOracle` diseñado para relay autenticado de MIC/DTA (validado tras confirmar la ausencia de APIs públicas en las aduanas de Bolivia y Chile).
- **Frontend:** Next.js 16 + React 19 + Viem, diseño cyber-fintech bilingüe (selector EN/ES).
- **Hardware Criptográfico:** Tarjeta Tangem NFC (chip con certificación de seguridad EAL6+, firmas EIP-712 en hardware) con fallback a firma MetaMask para entornos de escritorio.

---

## ⚡ ¿Por qué Avalanche Fuji?

La logística física no puede tolerar demoras de 15 minutos en un muelle de carga:
- **Finalidad sub-segundo:** La liquidación ocurre en el instante exacto del tap físico.
- **Costos predecibles e ínfimos:** Transacciones por menos de una milésima de dólar.
- **Confianza institucional probada:** Instituciones financieras líderes como **Franklin Templeton**, **KKR** y **JPMorgan (Onyx)** ya han validado la liquidación de activos reales en la red Avalanche.

---

## 🚀 Instalación y Ejecución Local

### Prerrequisitos
- Node.js >= 20
- Foundry (`forge`, `cast`)

### 1. Smart Contracts
```bash
cd contracts
forge build
forge test
```

### 2. Frontend (dApp)
```bash
cd frontend
npm install
npm run dev
```
Abre [http://localhost:3000](http://localhost:3000) en tu navegador.

---

## 📚 Documentación Técnica

- [**PITCH-OFICIAL.md**](PITCH-OFICIAL.md) — Guion oficial de 2 minutos para presentación ante jurados.
- [**CRONOLOGIA-Y-ARQUITECTURA.md**](CRONOLOGIA-Y-ARQUITECTURA.md) — Registro histórico completo del proyecto, decisiones técnicas y trade-offs.
- [**docs/ARCHITECTURE.md**](docs/ARCHITECTURE.md) — Especificación de arquitectura, estados del escrow y riesgos.
- [**docs/DIAGRAMA_FLUJO_DATOS.md**](docs/DIAGRAMA_FLUJO_DATOS.md) — Diagrama de secuencia y flujo de datos on-chain ([versión interactiva HTML](docs/diagrama_flujo_datos.html)).
- [**docs/GASLESS-RELAYER.md**](docs/GASLESS-RELAYER.md) — Detalle técnico del flujo gasless (ERC-2771 + ERC-2612).
- [**docs/CUSTOMS-ORACLE.md**](docs/CUSTOMS-ORACLE.md) — Arquitectura y rol del oráculo de aduana.
- [**docs/JUSTIFICACION-AVALANCHE-CASOS-REALES.md**](docs/JUSTIFICACION-AVALANCHE-CASOS-REALES.md) — Investigación de casos reales de uso de Avalanche.
- [**docs/JUSTIFICACION-UNLOCK-POLLAR-CASOS-REALES.md**](docs/JUSTIFICACION-UNLOCK-POLLAR-CASOS-REALES.md) — Justificación de Pollar y Unlock Protocol.
- [**docs/FLUJO-REAL-DEMO.md**](docs/FLUJO-REAL-DEMO.md) — Auditoría de componentes on-chain de la demo en vivo.
- [**docs/CORRIDA-REAL-DE-CARGA.md**](docs/CORRIDA-REAL-DE-CARGA.md) — Registro de balances, billeteras y despliegue real en testnet.

---

## 🏆 Tracks de la Buildathon

- **Avalanche:** Contrato `TradeEscrow` desplegado, verificado y activo en Fuji Testnet con frontend conectado.
- **Pollar:** Rampa de entrada (*on-ramp*) en bolivianos vía QR bancario sin fricción criptográfica para importadores locales.
- **Unlock Protocol:** Arquitectura para certificación NFT de transportistas auditados y seguros vehiculares con expiración on-chain dinámica (`PublicLock`).

---

## 👥 Equipo ArquiSoft

- **Ronald Augusto** — *Arquitectura de Negocio, Pitch & Documentación*
- **Dax Kenji** — *Smart Contracts & Backend*
- **Víctor Murillo** — *Frontend & Web3 Integration*
- **Ariane** — *Diseño UX/UI & QA*
- **Amira** — *Frontend Engineering*

---

## 📄 Licencia

Este proyecto está bajo la Licencia [MIT](LICENSE).

# 📜 Cronología Técnica y Arquitectura de RoutePay

> **Documento de Registro Histórico y Ficha Técnica del Proyecto**  
> Buildathon Ethereum Bolivia 2026 (Cochabamba)  
> Proyecto: **RoutePay (AndesCargo Protocol)** — *Smart Trade & Logistics Escrow con Liquidación Criptográfica*

---

## 1. 🎯 Resumen Ejecutivo

**RoutePay** es un protocolo de fideicomiso descentralizado (*escrow*) diseñado para resolver el problema de desconfianza, falta de divisas y pagos diferidos en el comercio terrestre transfronterizo de Sudamérica (específicamente en el corredor **Arica [Chile] ➔ Tambo Quemado ➔ Santa Cruz [Bolivia]**).

El protocolo asegura el pago del flete y la carga en stablecoins (USDC/USDT), permitiendo que:
1. El **importador** fondee la orden de manera *gasless* (ERC-2771 + ERC-2612) o pague en moneda local (BOB) mediante QR bancario vía **Pollar**.
2. La aduana certifique el tránsito mediante un **Customs Oracle**.
3. El **transportista** cobre su pago de forma instantánea al entregar la carga físicamente mediante un **tap de tarjeta NFC (Tangem)** que genera una firma criptográfica ECDSA (`secp256k1`) validada on-chain.
4. Las licencias y seguros del transportista se auditen dinámicamente mediante **Unlock Protocol** (NFTs con caducidad).

---

## 2. ⏳ Cronología de Evolución del Proyecto

### Etapa 1: Análisis de Problemáticas y Validación de Campo (Fase 1)
- **Problemáticas evaluadas:**
  - **P1:** Remesas y Ahorro contra el cepo cambiario boliviano.
  - **P2:** Liquidación de exportaciones agropecuarias.
  - **P3/T5:** Fletes y pagos transfronterizos en logística terrestre internacional.
- **Validación Mom Test & Estudio de Campo:**
  - Se identificó que el dolor más agudo, cuantificable e inmediato no era una simple billetera de ahorro, sino el calvario de los importadores y choferes de camiones en la ruta Arica–Santa Cruz: demoras bancarias de hasta 15 días para liberar pagos, cobros en efectivo con riesgo de asalto en carretera, y pérdidas del 8 al 12% por comisiones bancarias y brecha cambiaria.
- **Prototipo temprano descartado:**
  - Se implementó inicialmente un prototipo de bóveda de ahorro (`SavingsVault.sol`, ERC-4626) para HSK Chain, pero se descartó estratégicamente al descubrir que el impacto real, el encaje de bounties y la viabilidad técnica estaban en la logística transfronteriza (T5).

### Etapa 2: Decisión Estratégica y Desacoplamiento de Repositorios (Fase 2)
- **Pivote Oficial a RoutePay (AndesCargo):**
  - Se definió el alcance del MVP: 1 contrato inteligente sólido con patrón Checks-Effects-Interactions (`TradeEscrow.sol`), soporte de gasless relayer, y verificación física por hardware NFC.
- **Desacoplamiento de Arquitectura:**
  - `cocha-blockchain`: Mantenido como repositorio interno de planificación, actas, estudios de mercado y guiones de pitch.
  - `routepay`: Creado en la organización `RoutePay-Protocol` como repositorio público, auditable y limpio con el código fuente del protocolo (Foundry + Next.js App Router).

### Etapa 3: Desarrollo de Smart Contracts e Infraestructura Criptográfica
- **Scaffolding e Implementación Base (`0b591d7`, `a1dd6ee`):**
  - Creación de `TradeEscrow.sol` bajo Solidity 0.8.24 y OpenZeppelin Contracts 5.x.
  - Configuración del toolchain Foundry (`forge`, `cast`, `anvil`).
  - Implementación del ciclo de vida de la orden: `Created`, `Funded`, `InTransit`, `Completed`, `Refunded`, `Disputed`.
- **Integración de Aduana y Gasless Flow (`d7b2def`, `17c6b78`):**
  - Implementación del rol `customsOracle` para validar el paso de frontera (tránsito MIC/DTA).
  - Implementación de meta-transacciones ERC-2771 (vía `ERC2771Forwarder`) y `permit` ERC-2612 para que el importador no requiera gas nativo para fondear órdenes.
- **Robustez y Seguridad (`bbe956d`):**
  - Cobertura completa en Foundry con 19 tests unitarios y fuzz tests:
    - Verificación contra ataques de reentrancia (`ReentrancyGuard`).
    - Imposibilidad de doble liquidación (`RevertIfDoubleSettle`) y doble reembolso (`RevertIfDoubleRefund`).
    - Validación matemática del 0.5% de comisión del protocolo hacia la tesorería.

### Etapa 4: Frontend, Hardware NFC y UX Real
- **Inicialización de la dApp (`8d672d1`, `db7076d`):**
  - Next.js 15 (App Router), Tailwind CSS, Viem y Wagmi.
  - Diseño estilo cyber-fintech enfocado en transportistas e importadores con pantallas de alta visibilidad en carretera.
- **Firma Real con Tangem y Deep-Linking Móvil (`a942552`, `8f356e9`):**
  - Se conectó la firma física de tarjeta NFC (o simulación de firma de clave privada vía MetaMask en caso de no contar con hardware NFC físico en iOS).
  - Deep-linking a MetaMask móvil para evitar fallos de inyección de proveedor en navegadores móviles.
- **Integración de Bounties (`c2e9efc`, `9b53a4e`):**
  - Modal de pago QR bancario en bolivianos vía Pollar.
  - Sistema de certificación de seguros de transporte con Unlock Protocol.

### Etapa 5: Despliegue en Testnet y Verificación Pública
- **Despliegue en Avalanche Fuji (`4275abb`, `cfe3e00`):**
  - Despliegue de contratos v2 en Avalanche Fuji Testnet.
- **Verificación de Código Abierto (`622cfe5`, `4ab038d`):**
  - Verificación formal de código fuente con coincidencia exacta (exact-match) en **Sourcify** y en **SnowTrace**.

---

## 3. 🏗️ Matriz de Decisiones de Arquitectura (Trade-offs)

| Componente | Opción Elegida | Alternativa Descartada | Justificación Técnica del Senior Architect |
|---|---|---|---|
| **Red de Ejecución** | **Avalanche Fuji** | Ethereum L1 / Arbitrum | Finalidad sub-segundo (< 1s) requerida para la experiencia de entrega "Tap & Go". Respaldo institucional demostrado (Franklin Templeton, KKR, JPMorgan Onyx). |
| **Moneda de Fondeo** | **USDC / Stablecoins** | Cripto volátil (AVAX, ETH) | El flete de carga no tolera volatilidad de precios en un viaje de 4 días. |
| **Onboarding Importador** | **ERC-2771 Gasless + Pollar QR** | Compra tradicional en exchanges | El importador boliviano no tiene acceso a exchanges bancarizados. Pollar permite cobrar en BOB y el Forwarder elimina la necesidad de comprar AVAX para gas. |
| **Validación de Entrega** | **Firma NFC Tangem (`secp256k1`)** | Código QR o PIN por SMS | Un SMS no funciona en pasos fronterizos sin señal celular. Un chip Tangem firma criptográficamente offline y el contrato verifica la firma en el momento del sync. |
| **Auditoría de Choferes** | **Unlock Protocol** | Registro centralizado SQL | Permite membresías con vencimiento automático on-chain (licencia o seguro de carga), revocando la autorización si la póliza caduca. |

---

## 4. 🔬 Especificación de Smart Contracts

### `TradeEscrow.sol`
- **Compilador:** Solidity `0.8.24` (EVM Paris/Shanghai).
- **Librerías:** OpenZeppelin `ERC2771Context`, `ReentrancyGuard`, `SafeERC20`, `ECDSA`, `MessageHashUtils`.
- **Estructura de la Orden (`Order`):**
  ```solidity
  struct Order {
      bytes32 orderId;
      address importer;
      address carrier;
      address token;
      uint256 amount;
      uint256 fee;
      uint256 deadline;
      OrderStatus status; // Created, Funded, InTransit, Completed, Refunded, Disputed
  }
  ```
- **Seguridad y Auditoría Interna:**
  - Patrón **CEI (Checks-Effects-Interactions)** estricto en `settleOrder()` y `refundOrder()`.
  - Errores personalizados (`custom errors`) en lugar de strings de revert para ahorro sustancial de gas:
    - `OrderAlreadyExists()`, `OrderNotFound()`, `InvalidStatus()`, `Unauthorized()`, `InvalidSignature()`, `DeadlineNotPassed()`.

---

## 5. 📊 Estado de Verificación y Enlaces en Vivo

- **Repositorio Oficial:** [`RoutePay-Protocol/routepay`](https://github.com/RoutePay-Protocol/routepay)
- **Red:** Avalanche Fuji Testnet (Chain ID `43113`)
- **Contratos Desplegados y Verificados:**
  - `TradeEscrow`: Código fuente 100% verificado en SnowTrace y Sourcify.
- **Suite de Pruebas:**
  - `forge test`: **19 tests ejecutados, 19 tests pasados (100% éxito)**.
- **Frontend:**
  - Next.js 15, conectado a Wagmi v2 con soporte móvil y fallback.

---

## 6. 🏆 Resumen para Pitch y Defensa (3 Minutos)

1. **El Problema (0:00 - 0:45):** "En el corredor Arica-Santa Cruz, un transportista boliviano tarda hasta 15 días en cobrar su flete por el cepo y la burocracia bancaria, perdiendo el 10% en intermediarios."
2. **La Solución (0:45 - 1:30):** "RoutePay: un escrow en Avalanche Fuji donde el importador fondea en bolivianos vía QR (Pollar) sin pagar gas, y el transportista cobra al instante de descargar con un tap de su tarjeta NFC."
3. **Ingeniería Real (1:30 - 2:30):** "No es una maqueta: 19 tests en Foundry, meta-transacciones ERC-2771 reales, oráculo aduanero modelado, y contratos verificados en SnowTrace."
4. **Cierre (2:30 - 3:00):** "0.5% de comisión por transacción. Avalanche para velocidad, Pollar para moneda local, Unlock para seguros. Infraestructura real para comercio real."

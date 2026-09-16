# 🎤 Pitch Oficial — RoutePay Protocol (2 Minutos)

**Equipo:** ArquiSoft  
**Proyecto:** RoutePay Protocol  
**Evento:** ETH Bolivia Buildathon 2026 (Cochabamba)  
**Tiempo Total:** 2:00 minutos exactos (120 segundos)  

---

## 1. Problemática (0:00 – 0:25)

"En el comercio exterior boliviano, en el corredor crítico Arica hacia nuestro país, el sistema de transporte pesado está quebrado.
El transportista asume combustible y viáticos de su bolsillo, para terminar cobrando su flete 30 a 60 días después y expuesto a estafas con comprobantes falsos.
Al mismo tiempo, el importador sufre por la falta de dólares físicos y paga entre 8% y 12% en comisiones bancarias que tardan semanas. La logística boliviana está asfixiada por la banca tradicional."

---

## 2. Solución (0:25 – 0:45)

"Para resolverlo creamos RoutePay Protocol: un Smart Trade & Logistics Escrow.
El importador custodia el flete en dólares digitales antes de que el camión ruede. Ese pago se libera de forma instantánea e irrevocable en el segundo exacto en que el encargado del almacén receptor apoya una tarjeta física Tangem NFC contra el celular del transportista. Cero intermediarios y con prueba de presencia física."

---

## 3. Arquitectura y Stack Estratégico (0:45 – 1:15)

"Nuestra arquitectura técnica integra tres pilares del ecosistema:

* **¿Por qué Avalanche?:** La logística física no puede esperar 15 minutos en un muelle; Avalanche Fuji nos da finalidad sub-segundo y costos menores a una milésima de dólar. Además, mediante ERC-2612 Permit y ERC-2771, importador y chofer tienen una experiencia cero gas.
* **¿Por qué Unlock Protocol?:** Nadie confía una carga de cincuenta mil dólares a una wallet anónima. Con un contrato PublicLock emitimos credenciales NFT intransferibles que certifican camión y seguro, con expiración nativa on-chain si la póliza vence.
* **¿Por qué Pollar?:** Conectamos el on-ramp para que el importador pague en bolivianos vía QR bancario sin lidiar con llaves ni exchanges."

---

## 4. Competencia: Por qué somos mejores (1:15 – 1:35)

"¿Por qué somos superiores a las alternativas existentes?

* **Frente a SWIFT y los bancos:** Liquidamos en un segundo y no en 40 días, eliminando la comisión del 10%.
* **Frente a un escrow web tradicional:** Un botón en una página se puede hackear o presionar a distancia. La tarjeta Tangem cuenta con chip EAL6+: la firma criptográfica EIP-712 se genera dentro del plástico, garantizando que el receptor estaba físicamente frente a la carga descargada."

---

## 5. Rentabilidad y Escalabilidad (1:35 – 1:50)

"¿Cómo es viable el negocio?

* **Rentabilidad:** Cobramos un micro-fee transparente del 0.5% por liquidación. En un flete de 2.500 dólares, el chofer recibe 2.487 netos y 12.50 van a tesorería —la banca le sacaría 250 dólares.
* **Escalabilidad:** Esta arquitectura es agnóstica y escala de inmediato a los corredores de carga de Chile, Perú, Argentina y Paraguay."

---

## 6. Cierre (1:50 – 2:00)

"Somos **ArquiSoft** y hemos creado **RoutePay**."

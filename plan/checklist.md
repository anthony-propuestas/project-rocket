# Project Rocket — Checklist de avance

Fuente: [guia-de-arranque.md](guia-de-arranque.md). Marcá `[x]` al terminar cada paso.
Regla de oro: no pases de fase sin cumplir su gate.

---

## Día 0 · 20–21 oct — Herramientas y cuentas

### 0.1 Wallet
- [x] Crear wallet nueva solo para el proyecto (MetaMask o Rabby)
- [x] Anotar la seed en papel, offline
- [X] Agregar Robinhood Chain Testnet (chain ID 46630)
- [X] Conseguir ETH de testnet (faucet)

### 0.2 Entorno de desarrollo
- [X] Instalar VS Code
- [x] Instalar Git y configurar nombre/email
- [x] Instalar Node LTS
- [x] Instalar Foundry (`foundryup`) y verificar con `forge --version`

### 0.3 Repo
- [x] Crear/usar cuenta de GitHub
- [x] Crear repo público `project-rocket` con licencia MIT
- [x] Clonar el repo y hacer `git init` de esta carpeta (subir `plan/`)

### 0.4 Marca y redes
- [ ] Abrir cuenta de X con la marca Project Rocket
- [ ] Abrir grupo/canal de Telegram
- [ ] Abrir servidor de Discord

### 0.5 Contexto para IA
- [ ] Subir al proyecto de Claude el white paper
- [ ] Subir el roadmap
- [ ] Subir los eslóganes

---

## Fase 1 · 20 oct – 2 nov — Validación

### Semana 1 (20–26 oct)

**1.1 ENS**
- [x] Comprar `projectrocket.eth`
- [ ] Anotar fecha de vencimiento y poner recordatorio de renovación

**1.2 Landing con waitlist**
- [ ] Crear cuenta de Cloudflare
- [ ] Crear formulario de waitlist gratis (Tally o similar)
- [ ] Escribir copy de la landing: slogan + qué es + CTA a la waitlist
- [ ] Armar la página (HTML estático de una página)
- [ ] Publicar en Cloudflare Pages (`projectrocket.pages.dev`)
- [ ] Probar el formulario de punta a punta (alta de prueba)

**1.3 Análisis de competencia**
- [ ] pump.fun: umbral de graduación, fees, reglas anti-sniper
- [ ] Pons: umbral, fees, reglas
- [ ] FOMO: umbral, fees, reglas, requisitos de verificación de tokens
- [ ] Armar tabla comparativa y anotar qué diferencia a Project Rocket

**1.4 Legal — cotización**
- [ ] Buscar 2–3 abogados con experiencia en CNV/PSAV
- [ ] Preparar resumen de 1 página del proyecto para enviarles
- [ ] Pedir cotización a cada uno
- [ ] Elegir abogado (tope $800) y agendar consulta para la semana 2

**1.5 Entrevistas — preparación** (adelantado para llegar a 10 en sem 2)
- [ ] Escribir guion de entrevista (por qué abandonan un token, valoran reglas anti-sniper, etc.)
- [ ] Armar lista de ≥20 candidatos (traders y creadores de Robinhood Chain en X y Telegram)
- [ ] Crear planilla para registrar respuestas

### Semana 2 (27 oct – 2 nov)

**1.6 Entrevistas**
- [ ] Contactar candidatos y agendar
- [ ] Entrevistas 1–5
- [ ] Entrevistas 6–10
- [ ] Resumir resultados: cuántos valoran el goteo (meta ≥5/10)

**1.7 Consulta legal**
- [ ] Preparar preguntas: registro PSAV y excepción de la RG 1172/2026
- [ ] Hacer la consulta
- [ ] Documentar la conclusión (¿hay bloqueo? ¿qué condiciones?)

**1.8 Comunicación**
- [ ] Publicar el white paper en el repo
- [ ] Primeros posts en X con los eslóganes y link a la waitlist
- [ ] Contar inscriptos en la waitlist (meta ≥50)

### ✅ Gate 1 · 2 nov — Demanda + legal
- [ ] ≥5 de 10 entrevistados valoran el goteo
- [ ] ≥50 personas en la waitlist
- [ ] El abogado no ve bloqueo
- [ ] Si algo falla: ajustar la idea antes de codear y documentar el cambio

---

## Fase 2 · 3 – 16 nov — Diseño de la mecánica

### Semana 3 (3–9 nov)

**2.1 Simulación base (Python en Google Colab)**
- [ ] Implementar la curva constant-product con reservas virtuales
- [ ] Funciones de compra y venta con fee del 1%
- [ ] Graficar precio vs. ETH acumulado

**2.2 Calibración**
- [ ] Probar reserva virtual inicial ~6,3 ETH
- [ ] Verificar que 20 ETH den ~$250k de MC (ETH a $2.400)
- [ ] Ajustar hasta que el precio final de la curva coincida con el del pool

**2.3 Goteo en la simulación**
- [ ] Snipe tax 50% → 1% en ~3 min
- [ ] Lock de venta 30 min + cooldown de recompra 30 min
- [ ] Fee de venta 5% → 1% en 7 días (tiempo promedio ponderado de compra)
- [ ] Tope 2% del supply por wallet

**2.4 Simular ataques**
- [ ] Sniper del primer bloque (debe perder plata)
- [ ] Vender y recomprar
- [ ] Sybil con 10 wallets

**2.5 Matemática de la graduación**
- [ ] Calcular ETH acumulado: curva + 50% de fees + penalidades + fees de creación
- [ ] Calcular tokens que van al pool y tokens a quemar
- [ ] Verificar precio del pool = precio final de la curva
- [ ] Resolver lo pendiente: "pool sin salto de precio" (depositar todo el ETH, solo los tokens necesarios, quemar el sobrante)
- [ ] Documentar parámetros finales y el porqué de cada ajuste

### Semana 4 (10–16 nov)

**2.6 Confirmar parámetros**
- [ ] Confirmar con la simulación goteo, fees y graduación de la tabla de decisiones
- [ ] Congelar la tabla de parámetros (no cambian después del deploy)

**2.7 Elegir base de código** (antes de la spec, para que la spec encaje)
- [ ] Listar launchpads open source auditados
- [ ] Filtrar por licencia (solo MIT, BSD o Apache-2.0; nada GPL ni BUSL)
- [ ] Verificar que encaje con el singleton y el token que solo se mueve con la curva
- [ ] Decidir: fork (conservando avisos de copyright) u OpenZeppelin + curva propia

**2.8 Escribir la spec en el repo**
- [ ] Goteo por beneficiario e interruptor de graduación
- [ ] Activo de contrapartida por curva, lista fija de activos por versión
- [ ] Tiempo promedio ponderado de compra y retiro proporcional
- [ ] Modo solo-retiro y reembolso saltean el lock de venta
- [ ] Destino de cada fee: 50/50 creador/liquidez, creación y penalidades 100% a liquidez, 0% plataforma, sin fondo de seguridad
- [ ] Posición LP sin funciones de retiro ni de cobro
- [ ] Reembolso a 1 día si falla la graduación
- [ ] Topes de v1: 100 ETH globales, 0,5 ETH por compra
- [ ] Funciones y eventos de cada contrato
- [ ] Invariantes (ej.: el saldo real cubre lo contabilizado)

### ✅ Gate 2 · 16 nov — Parámetros fijos
- [ ] Parámetros fijados
- [ ] Spec escrita en el repo
- [ ] La simulación muestra que el sniper pierde plata
- [ ] La matemática de la graduación cierra

---

## Fase 3 · 17 nov – 14 dic — Construcción

### Semana 5 (17–23 nov) — Token + curva
- [ ] Inicializar proyecto Foundry e instalar OpenZeppelin
- [ ] Token ERC-20 (solo se mueve con la curva antes de graduar)
- [ ] Contrato singleton: crear token (fee de creación 0,001 ETH)
- [ ] Comprar en la curva (ETH → WETH)
- [ ] Vender en la curva
- [ ] Activo de contrapartida guardado por curva; lista de activos fija en el deploy (v1: solo WETH)
- [ ] Rechazar tokens con fee al transferir o saldos que cambian solos
- [ ] Fee 1%: 50% creador, 50% liquidez
- [ ] Tests Foundry de todo lo anterior pasando

### Semana 6 (24–30 nov) — Librerías de goteo
- [ ] Librería snipe tax + tests unitarios + fuzz
- [ ] Librería lock de venta / cooldown / fee de venta decreciente (tiempo promedio ponderado) + tests + fuzz
- [ ] Librería tope 2% por wallet + tests + fuzz
- [ ] Aplicar las reglas al beneficiario, no al llamador (test con router)
- [ ] Integrar las 3 librerías en el singleton

### Semana 7 (1–7 dic) — Graduación + seguridad de fondos
- [ ] `graduate()`: chequeo de 20 ETH + ≥300 holders + top 10 <30%
- [ ] Crear pool Uniswap v4 sin hook y LP bloqueada sin cobro de fees
- [ ] Quemar tokens sobrantes; interruptor a ERC-20 clásico
- [ ] Modo solo-retiro con retiro proporcional
- [ ] Reembolso si la graduación falla por 1 día
- [ ] Topes de v1 (100 ETH globales, 0,5 ETH por compra)
- [ ] Tests de todo lo anterior
- [ ] `forge build --sizes`: si pasa 24 KB, separar `RocketGraduator`
- [ ] Deploy en testnet

### Semana 8 (8–14 dic) — Frontend
- [ ] Proyecto Vite + wagmi/viem + RainbowKit
- [ ] `contracts.json` con v1 y la versión por defecto
- [ ] Lectura de datos: eventos + Blockscout
- [ ] Página: ranking de tokens
- [ ] Página: detalle de token (comprar/vender)
- [ ] Página: crear token
- [ ] Publicar en IPFS y en pages.dev

### ✅ Gate 3 · 14 dic — Ciclo en testnet
- [ ] Script de graduación con ≥300 wallets y umbrales reales
- [ ] Ciclo completo en testnet: crear → comprar → graduar → operar en Uniswap

---

## Fase 4 · 15 – 28 dic — Seguridad y beta

### Semana 9 (15–21 dic)
- [ ] GitHub Actions con Slither en cada commit
- [ ] Sumar Aderyn al workflow
- [ ] Revisar y resolver hallazgos de ambos
- [ ] Invariant tests de Foundry sobre la spec
- [ ] Postular a CodeHawks First Flights
- [ ] Aplicar a Uniswap Foundation Security Fund
- [ ] Aplicar al programa de seguridad de Arbitrum
- [ ] Revisar fechas de la próxima season de Zealynx

### Semana 10 (22–28 dic)
- [ ] Invitar a la waitlist a la beta en testnet (meta ≥20 testers)
- [ ] Canal para reportar bugs (Discord)
- [ ] Triage y corrección de bugs reportados
- [ ] Rocket Academy: setup VitePress
- [ ] Rocket Academy: módulo 1
- [ ] Rocket Academy: módulo 2
- [ ] Build estático de Academy en IPFS

### ✅ Gate 4 · 28 dic — 0 bugs críticos
- [ ] Cero bugs críticos o altos abiertos
- [ ] 20 transacciones de testers reales en testnet sin fallas

---

## Fase 5 · 29 dic – 10 ene — Lanzamiento

### Semana 11 (29 dic – 4 ene)
- [ ] Fondear wallet de deploy con ETH en mainnet (gas hasta $200)
- [ ] Deploy en mainnet de v1 con topes
- [ ] Verificar contratos en Blockscout
- [ ] Actualizar `contracts.json` con direcciones de mainnet
- [ ] Publicar frontend en IPFS y apuntar `projectrocket.eth` a IPNS
- [ ] Publicar el CID en el repo
- [ ] Prueba de humo en mainnet: crear token y comprar con monto mínimo

### Semana 12 (5–10 ene)
- [ ] Crear `academy.projectrocket.eth` y apuntarlo al CID/IPNS de Academy
- [ ] Preparar anuncio + adelanto de lo que viene en Academy
- [ ] **10 ene:** anuncio en X, Telegram y Discord
- [ ] Armar planilla de métricas semanales: tokens creados, tasa de graduación, holders al graduar, % de supply de snipers en 5 min, holders retenidos a 7 días, MC 30 días post-graduación, alumnos de Academy, incidentes

### 🚀 Beta v1 en mainnet · 10 ene 2027

---

## Después del MVP (hasta ~US$25k, con grants y donaciones)

**Meses 3–6**
- [ ] Auditoría paga con auditor independiente (hasta $15k)
- [ ] Bounty propio en GitHub
- [ ] v2 sin topes como deploy nuevo
- [ ] Rocket Academy: módulos 3–5, Starter Kit y certificado on-chain

**Meses 6–12**
- [ ] USDG como contrapartida
- [ ] Token $ROCKET
- [ ] v3: criterio de holders afinado, alertas y perfiles verificados

**Sin fecha**
- [ ] Acciones tokenizadas (solo con aval legal)
- [ ] Con tracción: pedir a FOMO que integre los tokens graduados

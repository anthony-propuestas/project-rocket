# Project Rocket — Guía de arranque paso a paso

Oct 20, 2026 · @AnthonyAsuaje

## Resumen

Arrancás de cero el 20 oct 2026 y la meta es la v1 del proyecto: una beta en mainnet de Robinhood Chain el 10 ene 2027: 12 semanas, 5 fases y 4 gates. El MVP cuesta hasta ~US$1.200 (legal hasta $800 + gas hasta $200 + ~$200 de margen; el ENS ya está pago) y lo hacés solo, con herramientas gratis y ayuda de IA.

Regla de oro: no pases de fase sin cumplir su gate. Los criterios de cada gate son una propuesta; ajustalos si hace falta.

El avance se marca en [checklist.md](checklist.md); esta guía es el detalle y el porqué de cada paso.

**Cinco fases y cuatro gates hasta la beta del 10 ene** — cada gate se cumple antes de pasar a la fase siguiente.

| Fase | Fechas | Entregables | Gate |
|---|---|---|---|
| Validación | 20 oct – 2 nov | Landing · 10 entrevistas · Consulta legal | Gate 1 · 2 nov — demanda + legal |
| Mecánica | 3 – 16 nov | Simular la curva · Fijar el goteo · Spec en el repo | Gate 2 · 16 nov — parámetros fijos |
| Construcción | 17 nov – 14 dic | Curva + goteo · Graduación a v4 · Frontend en IPFS | Gate 3 · 14 dic — ciclo en testnet |
| Seguridad | 15 – 28 dic | Slither + fuzzing · First Flights · Beta en testnet | Gate 4 · 28 dic — 0 bugs críticos |
| Lanzamiento | 29 dic – 10 ene | Mainnet v1 con topes · projectrocket.eth → IPNS · Rocket Academy (invitación) | Beta v1 en mainnet — 10 ene 2027 |

*plan MVP · 5 fases, 4 gates*

Cada fase cierra con un gate; si no se cumple, se ajusta antes de seguir.

## Decisiones ya tomadas

Esto sale del white paper y los chats del proyecto. No lo rediscutas: ejecutalo.

| Tema | Decisión |
|---|---|
| Chain | Robinhood Chain (L2 Arbitrum Orbit, chain ID 4663; testnet 46630) |
| Contrapartida | Solo ETH (WETH por dentro); USDG y $ROCKET después |
| Curva | Constant-product con reservas virtuales (como pump.fun); curva S descartada |
| Graduación | 20 ETH fijos en la curva (~$250k de MC) + ≥300 holders + top 10 con <30% del supply |
| Supply | 1.000M por token; ~80% en la curva, resto a liquidez |
| Goteo 1 | Snipe tax 50% → 1% en ~3 min |
| Goteo 2 | Lock de venta 30 min desde la última compra (toda la tenencia), cooldown de 30 min entre vender y recomprar mientras dure la curva, fee de venta 5% → 1% en 7 días |
| Goteo 3 | Tope 2% del supply por wallet durante la curva |
| Creador | Sin compra privilegiada: compra en la curva como cualquiera (snipe tax y tope 2%) |
| Fees | 1% por trade: 50% creador, 50% liquidez de graduación; 0% plataforma y 0% fee de interfaz en el frontend |
| Fee de creación | 0,001 ETH fija, 100% a la liquidez del propio token; el gas de crear lo paga el creador a la red |
| Penalidades | Snipe tax y fee de venta temprana: 100% a la liquidez al graduar |
| Fondo de seguridad | No existe: va contra la filosofía sin operador; esa parte va a liquidez |
| Contratos | 2, inmutables, sin admin, MIT; Solidity + Foundry + OpenZeppelin |
| Al graduar | Pool Uniswap v4 sin hook, LP bloqueada para siempre; las fees del pool quedan en la posición y nadie las cobra |
| Reembolso | Si la graduación falla por 1 día, se habilitan reembolsos proporcionales |
| Topes de v1 | Máximo 100 ETH entre todas las curvas y 0,5 ETH por compra; fijos en el código, v2 los quita |
| Frontend | Vite + wagmi/viem + RainbowKit, estático en IPFS; entrada projectrocket.eth (comprado, listo para usar) |
| Rocket Academy | En v1 es una invitación a aprender (módulos 1–2); los módulos 3–5, el Starter Kit y el certificado van post-MVP |

**Decisiones de mecánica (19–20 oct)**

- **Curva constant-product (20 oct):** reservas virtuales como pump.fun. Reemplaza a la curva S: la sigmoide necesita `exp`/`ln` en punto fijo (riesgo de precisión) y no hay referencia auditada. El tramo de entrada injusto lo compensa el snipe tax. La curva S queda descartada: la constant-product tiene más código auditado de referencia.
- **Umbral: 20 ETH fijos** en la curva, sin oráculo. Punto de partida: reserva virtual inicial ~6,3 ETH (r ≈ 4,17) → MC final ≈ 104 ETH ≈ $250k con ETH a $2.400. El MC en USD varía con el precio de ETH.
- **Goteo por beneficiario:** las reglas se aplican a la wallet que recibe y tiene los tokens, no al contrato que llama. Así nadie las esquiva comprando por un router o agregador, y un router no bloquea a todos sus usuarios.
- **ERC-20 clásico al graduar:** antes de graduar, el token solo se mueve con la curva. `graduate()` activa un interruptor sin vuelta atrás y sin dueño: desde ahí no hay fees, locks ni topes.
- **Sin fondo de seguridad (20 oct):** un fondo acumulado es algo que alguien termina administrando. La fee de trading queda 50% creador y 50% liquidez del token.
- **Fees del pool sin cobrar (20 oct):** la posición de Uniswap v4 no tiene función de retiro ni de cobro. Es lo más simple, deja menos superficie de ataque y no tiene beneficiario.
- **Pool sin salto de precio (pendiente):** con 20 ETH y los 200M tokens restantes, el pool arrancaría por debajo del precio final de la curva (harían falta ~20,9 ETH). Propuesta a confirmar en la simulación: depositar todo el ETH acumulado y solo los tokens necesarios para igualar el precio, y quemar el sobrante.
- **Topes de v1 (20 oct):** se usa un tope global de ETH en vez de un máximo de curvas activas, porque las curvas que nunca gradúan bloquearían los cupos para siempre.

## Día 0 (20–21 oct): herramientas y cuentas

En dos días dejás listo todo lo que vas a usar 12 semanas. Costo: $0.

- Wallet nueva solo para el proyecto (MetaMask o Rabby). Seed anotada en papel, offline.
- Cuenta de GitHub y repo público `project-rocket` con licencia MIT.
- Instalar VS Code, Git, Node LTS y Foundry (`foundryup`).
- Agregar Robinhood Chain Testnet (chain ID 46630) a la wallet y conseguir ETH de testnet.
- Abrir cuentas de X, Telegram y Discord con la marca Project Rocket.
- Subir al proyecto de Claude el white paper, el roadmap y los eslóganes, para tenerlos a mano.

## Fase 1 · Sem 1–2 (20 oct – 2 nov): validación

Confirmás que hay demanda y que no hay bloqueo legal antes de escribir código.

**Semana 1 (20–26 oct)**

- `projectrocket.eth` comprado en ENS y listo para usar. Renovar antes de que venza (cualquiera puede renovarlo).
- Landing de una página con slogan y waitlist en Cloudflare Pages (`projectrocket.pages.dev`). Formulario gratis tipo Tally.
- Análisis de competencia: Pons, FOMO y pump.fun. Anotar umbral de graduación, fees y reglas.
- Pedir cotización a 2–3 abogados por CNV/PSAV.

**Semana 2 (27 oct – 2 nov)**

- 10 entrevistas a traders y creadores de Robinhood Chain (X y Telegram). Preguntá por qué abandonan un token y si valoran reglas anti-sniper.
- Consulta legal (hasta $800): Registro PSAV y excepción de la RG 1172/2026.
- Publicar el white paper en el repo y los primeros posts con los eslóganes.

**Gate 1 (2 nov):** ≥5 de 10 entrevistados valoran el goteo, ≥50 en la waitlist y el abogado no ve bloqueo. Si falla, ajustá la idea antes de codear.

## Fase 2 · Sem 3–4 (3 – 16 nov): diseño de la mecánica

Salís con todos los parámetros fijados y una spec escrita. Después del deploy no se pueden cambiar.

**Semana 3 (3–9 nov)**

- Simulación en Python (Google Colab, gratis): curva constant-product con reservas virtuales.
- Calibrar las reservas virtuales (partida: ~6,3 ETH) para que 20 ETH den ~$250k de MC y el precio final de la curva coincida con el del pool.
- Simular ataques: sniper del primer bloque, vender y recomprar, Sybil con 10 wallets.
- Verificar la graduación con matemática antes de testnet: ETH acumulado (curva + 50% de fees + penalidades + fees de creación), tokens que van al pool, tokens quemados y precio del pool = precio final de la curva. Si algo no cierra, ajustar parámetros y documentar el porqué.

**Semana 4 (10–16 nov)**

- Confirmar con la simulación los valores de goteo, fees y graduación de la tabla de decisiones.
- Llevar a la spec el goteo por beneficiario y el interruptor de graduación.
- Llevar a la spec el activo de contrapartida por curva, la lista fija de activos por versión, el tiempo promedio ponderado de compra y el retiro proporcional.
- Llevar a la spec que el modo solo-retiro y el reembolso saltean el lock de venta.
- Llevar a la spec el destino de cada fee: 50/50 creador y liquidez, fee de creación y penalidades 100% a liquidez, 0% plataforma, sin fondo de seguridad.
- Llevar a la spec la posición LP sin funciones de retiro ni de cobro, el reembolso a 1 día y los topes de v1 (100 ETH globales, 0,5 ETH por compra).
- Escribir la spec en el repo: funciones, eventos e invariantes (ej.: el saldo real cubre lo contabilizado).
- Elegir launchpads open source auditados para forkear y sumar el goteo. Solo licencias MIT, BSD o Apache-2.0 (nada GPL ni BUSL), con sus avisos de copyright. El fork debe encajar con el singleton y el token que solo se mueve con la curva; si no, OpenZeppelin + curva propia. Lo modificado pierde la auditoría: va igual a fuzzing y revisión.

**Gate 2 (16 nov):** parámetros fijados, spec escrita, la simulación muestra que el sniper pierde plata y la matemática de la graduación cierra.

## Fase 3 · Sem 5–8 (17 nov – 14 dic): construcción

Una pieza por semana, siempre con tests antes de pasar a la siguiente.

| Semana | Qué construís |
|---|---|
| 5 (17–23 nov) | Token ERC-20 + contrato singleton con la curva (comprar/vender) + tests Foundry. El ETH se envuelve en WETH; cada curva guarda su activo de contrapartida como parámetro; la lista de activos permitidos queda fija en el deploy (en v1 solo WETH) y se rechazan tokens con fee al transferir o saldos que cambian solos |
| 6 (24–30 nov) | Las 3 librerías de goteo, cada una con tests unitarios y fuzz. La fee de venta usa el tiempo promedio ponderado de compra de cada wallet |
| 7 (1–7 dic) | `graduate()` a Uniswap v4, LP bloqueada sin cobro de fees, modo solo-retiro con retiro proporcional (cada holder devuelve sus tokens y recupera su parte de la curva), reembolso a 1 día y topes de v1. Medir tamaño con `forge build --sizes`: si pasa 24 KB, separar la graduación en `RocketGraduator`. Deploy en testnet |
| 8 (8–14 dic) | Frontend: ranking, página de token y crear token. Datos de eventos + Blockscout. `contracts.json` con v1 y la versión por defecto. Publicar en IPFS y pages.dev |

**Gate 3 (14 dic):** en testnet funciona el ciclo completo: crear → comprar → graduar → operar en Uniswap. La graduación se prueba por script, con ≥300 wallets y los umbrales reales.

## Fase 4 · Sem 9–10 (15 – 28 dic): seguridad y beta

Buscás bugs con herramientas gratis y usuarios reales antes de tocar mainnet.

**Semana 9 (15–21 dic)**

- Slither y Aderyn en cada commit (GitHub Actions).
- Invariant tests de Foundry sobre la spec.
- Postular el código a CodeHawks First Flights (gratis).
- Aplicar a grants de auditoría: Uniswap Foundation Security Fund y el programa de Arbitrum. Atento a la próxima season de Zealynx.

**Semana 10 (22–28 dic)**

- Beta en testnet con la waitlist (≥20 testers).
- Corregir lo que aparezca.
- Rocket Academy: módulos 1 y 2 en VitePress, build estático en IPFS, como invitación a aprender (todavía sin guía de desarrollo).

**Gate 4 (28 dic):** cero bugs críticos o altos abiertos y 20 transacciones de testers reales en testnet sin fallas.

## Fase 5 · Sem 11–12 (29 dic – 10 ene): lanzamiento

Salís a mainnet con topes fijados en el código, así un error no puede costar mucho.

**Semana 11 (29 dic – 4 ene)**

- Deploy en mainnet de v1 con topes: 100 ETH entre todas las curvas y 0,5 ETH por compra. Gas estimado: hasta $200.
- Verificar los contratos en Blockscout.
- Apuntar `projectrocket.eth` (ya comprado) a IPNS y publicar el CID en el repo.

**Semana 12 (5–10 ene)**

- Lanzamiento el 10 ene: anuncio en X, Telegram y Discord.
- Crear el subdominio `academy.projectrocket.eth` (solo gas), apuntarlo al CID/IPNS de Rocket Academy y publicarla como invitación a aprender: módulos 1–2 y un adelanto de lo que viene.
- Medir cada semana: tokens creados, tasa de graduación, holders al graduar, % del supply comprado por snipers en los primeros 5 min, holders retenidos a 7 días, MC 30 días post-graduación, alumnos de Rocket Academy e incidentes.

## Después del MVP

La etapa siguiente pide hasta ~US$25k, financiados con grants y donaciones.

- **Meses 3–6:** auditoría paga (hasta $15k) con un auditor independiente, bounty propio en GitHub y v2 sin topes como deploy nuevo. Rocket Academy completa: módulos 3–5, Starter Kit y certificado on-chain.
- **Meses 6–12:** USDG como contrapartida, $ROCKET y v3 (criterio de holders afinado, alertas y perfiles verificados).
- **Acciones tokenizadas:** solo con aval legal, sin fecha.
- **Con tracción:** pedir a FOMO que integre los tokens graduados.

Decisión (20 oct): 0% para la plataforma y para el frontend en todas las versiones, no solo en el MVP. Fuera del gas de la red, todo lo que se cobra va al creador o a la liquidez. Cobrar comisiones es un indicio de "control o influencia" bajo la RG 1172/2026. Todo se financia con grants y donaciones: buscarlos temprano es parte del plan.

## Fuentes

**Documentos y chats del proyecto**

- [White paper v0.1](white-paper-v0.1.md)
- Chat: roadmap y white paper
- Chat: costos MVP y post-MVP
- Chat: auditoría barata
- Chat: plan de éxito post-MVP
- Chat: compatibilidad con FOMO
- Chat: bonding curve sigmoide
- Chat: eslóganes

**Externas**

- ENS — precios · Foundry · CodeHawks First Flights
- Uniswap Foundation Security Fund · Zealynx grants
- FOMO — verificación de tokens

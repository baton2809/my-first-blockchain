# my-first-blockchain

Учебный репозиторий по блокчейн-разработке и финтех-трендам.

Покрывает ключевые темы ближайших 5-10 лет в финтехе и AI: токенизация реальных активов, agent payments, AI-powered DeFi.

---

## Проекты

### [Blockchain Core](./blockchain/)

Учебный блокчейн с нуля — от простейшей цепочки блоков до полноценной P2P-сети с майнингом, транзакциями и криптографической защитой.

**Стек:** Python + FastAPI + WebSockets + SQLite + ECDSA

---

### [RWA Token Explorer](./rwa-explorer/)

Дашборд для отслеживания рынка токенизированных реальных активов (Real World Assets).

Показывает топ токенизированных фондов (BUIDL, ONDO и др.), график роста TVL и сравнение yield с традиционными инструментами.

**Стек:** Next.js + The Graph SDK + Recharts + Tailwind

---

### [x402 Micro-API Gateway](./x402-gateway/)

Сервис, продающий доступ к данным за USDC микроплатежами через протокол x402 (HTTP 402 Payment Required).

Включает FastAPI-сервер и Python-агента, который умеет автоматически платить и ретраить запросы.

**Стек:** FastAPI + Web3.py + Base Sepolia + JWT

---

### [AI Wallet](./ai-wallet/)

Реализация архитектуры "propose → simulate → confirm" для DeFi операций по концепции Виталика Бутерина.

LLM принимает команду на естественном языке, генерирует calldata, Tenderly симулирует транзакцию — пользователь видит результат до подписи.

**Стек:** Next.js + Anthropic API + Tenderly API + ethers.js + ERC-4337

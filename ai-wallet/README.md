# AI Wallet (Vitalik Architecture)

Реализация полного цикла "propose → simulate → confirm" для DeFi операций на базе архитектуры, предложенной Виталиком Бутериным.

## Что делает

1. **LLM Agent** (Claude/GPT) принимает команду на естественном языке: *"swap 100 USDC на ETH через Uniswap"*
2. Генерирует calldata транзакции (ABI encoding)
3. **Tenderly Simulation API** — симулирует транзакцию без отправки, возвращает:
   - Изменения баланса
   - Gas estimate
   - Events/logs
   - Potential risks
4. **UI** показывает human-readable превью: "Ты получишь ~0.041 ETH, потратишь $2.3 на газ"
5. Только после confirm юзера — отправка через ERC-4337 смарт-кошелёк

## Стек

Next.js + Anthropic API + Tenderly API + ethers.js + ERC-4337 (Pimlico/Alchemy AA)

## Архитектура

```
┌─────────────┐    plan    ┌──────────────┐   simulate   ┌─────────────┐
│   LLM Agent │──────────▶│ Local Client │─────────────▶│  Simulator  │
└─────────────┘            └──────────────┘              └─────────────┘
                                                                │
                                                         human review
                                                                │
                                                                ▼
                                                         ┌─────────────┐
                                                         │  Hardware   │
                                                         │   Wallet    │
                                                         └─────────────┘
```

Ключевая идея: **LLM не имеет доступа к приватному ключу**. Он только генерирует calldata транзакции. Локальный клиент (типа Tenderly simulation) показывает результат до подписи.

Технически реализуется через **Account Abstraction (ERC-4337)** — смарт-кошельки с programmable validation logic.

## Пример флоу

```
Команда: "swap 100 USDC на ETH"
    ↓ LLM
calldata: 0x38ed1739...
    ↓ Tenderly Simulate
{
  "balance_changes": [{"token": "USDC", "delta": -100}, {"token": "ETH", "delta": +0.041}],
  "gas_used": 145000,
  "risk_flags": []
}
    ↓ UI Preview → User Confirms → Sign & Send
```

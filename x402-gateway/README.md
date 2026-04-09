# x402 Micro-API Gateway

Сервис, который продаёт доступ к данным за USDC микроплатежами через протокол x402.

## Что делает

1. FastAPI сервер с эндпоинтом `/data` — возвращает 402 если нет оплаты
2. Принимает USDC на Base testnet
3. Верифицирует транзакцию через Alchemy/Infura webhook
4. Выдаёт JWT на 24 часа
5. Python-клиент (агент), который умеет автоматически платить и ретраить

## Стек

FastAPI + Web3.py + Base Sepolia + JWT

## Контекст: что такое x402 Protocol

HTTP 402 — это давно забытый статус-код "Payment Required". Coinbase реанимировал его как протокол: агент делает запрос → сервер отвечает 402 с инструкцией по оплате → агент платит стейблкоином → получает доступ.

```
Agent → GET /api/data
Server → 402 Payment Required
        { "amount": "0.001", "currency": "USDC", "network": "base", "address": "0x..." }
Agent → on-chain tx → получает JWT/токен доступа
Agent → GET /api/data + Bearer token → 200 OK
```

**Как физически работает:**

```javascript
// Упрощённая схема x402
const response = await fetch('https://api.example.com/data');

if (response.status === 402) {
  const paymentRequest = await response.json();
  // { amount: "0.001 USDC", address: "0x...", network: "base" }

  const tx = await wallet.sendTransaction({
    to: paymentRequest.address,
    value: parseUnits(paymentRequest.amount, 6) // USDC = 6 decimals
  });

  // Повторяем запрос с proof of payment
  const data = await fetch('https://api.example.com/data', {
    headers: { 'X-Payment-Tx': tx.hash }
  });
}
```

**Идея клиента-агента:**

```python
class x402Agent:
    async def fetch_with_payment(self, url: str) -> dict:
        response = await self.get(url)
        if response.status == 402:
            payment_info = response.json()
            tx_hash = await self.wallet.pay(payment_info)
            return await self.get(url, headers={"X-Payment": tx_hash})
```

**Примечание:** ACP (OpenAI + Stripe) работает иначе — через традиционные рельсы Stripe, но с AI-агентами как покупателями. Более регуляторно чистый путь.

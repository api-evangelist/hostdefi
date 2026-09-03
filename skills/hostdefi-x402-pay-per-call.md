---
name: Pay per call with x402
description: Buy a single graded verdict (or any paid HostDeFi resource) with USDC over the x402 protocol — no key, no account, charged only on success.
api: openapi/hostdefi-x402-machine-payable-api-openapi.yml
operations: ['GET /v1/x402/pricing', 'GET /v1/x402/token-risk/{chain}/{address}', 'POST /v1/x402/token-risk']
---
# Pay per call with x402

1. Discover prices free: `GET https://hostdefi.com/api/v1/x402/pricing` (or the manifest at `https://hostdefi.com/.well-known/x402`). The x402 lane's paths carry no operationIds in the spec — address them by method + path.
2. Call the paid resource with no payment header, e.g. `GET /v1/x402/token-risk/{chain}/{address}`. You get HTTP 402 with an `accepts` array — USDC offers on Solana and Base/Polygon/Arbitrum/Avalanche where available (v1 body + v2 PAYMENT-REQUIRED header dual-stack).
3. Pay one offer with any standard x402 client (`x402-fetch`'s `wrapFetchWithPayment` does the whole loop) and retry with the `X-PAYMENT` header.
4. Trust the fair-charging contract: you are charged only when a result comes back. A 404 (unresolvable token, no route) and any 5xx are explicitly uncharged; the settlement receipt arrives in `X-PAYMENT-RESPONSE` and `meta.x402`.
5. Doing steady volume? One $5 x402 payment to `POST /v1/x402/keys` mints a 30-day Agent-plan key (5,000 calls, batch) delivered in the paid response — cheaper per call than the pay-as-you-go lane.
6. Same tools over MCP: point an MCP client at `https://hostdefi.com/api/v1/mcp` (15 tools; paid ones take an `x402_payment` argument).

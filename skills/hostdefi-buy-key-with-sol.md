---
name: Buy an API key with SOL and track quota
description: Purchase a Builder/Pro/Scale key by SOL transfer + signature redemption, then send it as x-api-key and watch quota via the usage endpoint.
api: openapi/hostdefi-keys-api-openapi.yml
operations: [postKeys, getUsage, getHealth]
---
# Buy an API key with SOL and track quota

1. Read the CURRENT plan table from `getHealth` (`GET /v1/health`) — SOL prices move; the docs table (0.15 SOL/week Builder, 0.5 SOL/month Pro, 1.5 SOL/year Scale) is indicative, `/v1/health` is authoritative.
2. Send the plan amount in SOL to the wallet published in the docs at https://hostdefi.com/docs/api/, then redeem: `postKeys` — `POST /v1/keys` with `{"signature": "<tx signature>", "plan": "week|month|year"}`.
3. A 202 means the payment is not confirmed yet — retry shortly. A 409 means that signature was already redeemed (signatures burn after one use). There is NO refund path: confirm plan choice before paying.
4. Store the returned `apiKey` (prefix `vx_`) immediately — it is shown exactly once.
5. Send it as the `x-api-key` header on every call. Check quota anytime with `getUsage` (`GET /v1/usage`) — plan, quota, used, remaining, expiresAt; every verdict response also carries `meta.remaining`.
6. Respect the per-key burst ceiling (~5 req/s) — a 429 body names which limit you hit and when it resets.

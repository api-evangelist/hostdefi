---
name: Scan a token for safety, free
description: Get HostDeFi's structured token-risk read for any Solana or EVM token with no key, no account, no payment — 100 checks/day per IP.
api: openapi/hostdefi-token-risk-api-openapi.yml
operations: [postTokenRisk, getTokenRisk, postScan]
---
# Scan a token for safety, free

1. Call `postTokenRisk` — `POST https://hostdefi.com/api/v1/token-risk` with body `{"query": "<address or exact name/ticker>"}`. If you already know the chain, use `getTokenRisk` — `GET /v1/token-risk/{chain}/{address}` (chains: solana, ethereum, bsc, base, arbitrum, optimism, polygon, avalanche). The free web-scanner twin is `postScan` (`POST /api/scan`).
2. Read `risk.graded` FIRST. It can be `false`: the engine refuses to grade a token that is too fresh or under-described — read `risk.notGradedReason` and the `signals` that ARE known. Never treat an ungraded token as safe.
3. `risk.unknowns` is a real list of what could not be verified (LP lock, deployer history). Silence is not a clean bill.
4. On Solana read `signals.launch.bundled`, `signals.holders.top10Pct` (curve-aware) and `signals.token2022Extensions`; on EVM read `signals.evmSecurity` (honeypot, sell tax, blacklist, proxy).
5. Track your budget in-band: `meta.remaining` on each 200. On 429 the body carries the reset time and an `x402` block for pay-per-call continuation — do not hammer retries.
6. `meta.checkedAt` stamps when the data was computed; `meta.partial: true` means one enriching source was unreachable and the verdict stands on what was available.

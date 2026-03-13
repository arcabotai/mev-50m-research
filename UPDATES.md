# MEV $50M Investigation — Updates

## Update 1 — March 13, 2026 (~02:00 UTC) — OFFICIAL AAVE RESPONSE

**Status: HIGH PRIORITY — Official statements from Aave**

### Stani Kulechov (Aave founder) official statement
Timestamp: March 12, 2026 @ 20:33 UTC
Tweet: https://x.com/StaniKulechov/status/2032193345414664659

Key points:
- Confirms the $50M swap went through the **Aave interface**
- The interface warned the user about extraordinary slippage/price impact
- User was required to **check a confirmation checkbox** before proceeding
- Trade was executed on a **mobile device**
- CoW Swap routers **functioned as intended**
- Aave will **refund $600K in fees** collected from the transaction
- Aave will attempt to make contact with the user
- Promises to investigate stronger safeguards going forward

### Martin Grabina (mgrabina) — technical clarification
Timestamp: March 12, 2026 @ 22:40 UTC
Tweet: https://x.com/mgrabina/status/2032225132605833371

Key technical details:
- The issue was **price impact (99%)**, not slippage
- The CoW Explorer shows the original quote: 50M USDT → ~140 AAVE (before fees/slippage)
- The user actually received a **0.7% surplus**, confirming CoW mechanics worked as designed
- User received 324 AAVE (slightly better than the 140 AAVE quoted, hence the surplus)
- The price impact warning **was displayed** and the checkbox **was checked**

### Stani follow-up clarification
Timestamp: March 12, 2026 @ 22:44 UTC
Tweet: https://x.com/StaniKulechov/status/2032226271736836518
- "The issue wasn't slippage, it was user accepting a quote with high price impact (while being warned and accepting to proceed)"

### Spanish-language thread analysis (tobalgarcia_)
Thread explaining the mechanics in Spanish — routing went through SushiSwap V2 AAVE/WETH pool.

### Victim wallet status
- Balance: 0.128 ETH (near-empty — no refund received as of check time)
- No incoming transactions detected after the incident block

### What this means for our investigation
1. **MEV framing may need revision** — Aave/Stani are framing this as a UI/UX failure, not a MEV extraction. The user accepted a bad quote.
2. **However**, the MEV bot (0x06CFf708...) still extracted ~$23M and Titan Builder kept ~$27.2M — the arbitrage opportunity was real and executed.
3. **Aave's $600K refund** is symbolic (fees only) — the bulk of the $50M went to MEV and market structure, not Aave.
4. **Titan Builder has NOT made a statement** about this specific incident as of monitoring time.
5. **The victim has NOT been publicly identified** — Aave says they will "try to make contact."

### Open questions
- Will Titan Builder follow their precedent of refunding "fat finger" trades (see July 2025: refunded $112K PulseChain incident)?
- Will the victim come forward publicly?
- Legal action potential: Binance-linked whale losing $50M+ — pressure on Coinbase (received Titan's profit)?

---

## Update 2 — March 13, 2026 (~03:54 UTC) — COW PROTOCOL OFFICIAL STATEMENT

**New since Update 1: CoW Protocol published their own statement (not captured previously)**

### CoW Protocol / CoW DAO official statement
Timestamp: March 13, 2026 @ 01:21 UTC
Tweet: https://x.com/CoWSwap/status/2032265762916757614

Key points:
- Confirms **no exploit or malicious behavior** — transaction executed per signed order parameters
- States that **no DEX, aggregator, or liquidity pool** could have filled this at a reasonable price
- "Preventing users from making trades removes choice and can lead to terrible outcomes"
- CoW DAO will **also refund their fees** from this transaction (separate from Aave's $600K)
- Reviewing how to balance "strong safeguards with preserving user autonomy"

### Combined refund picture
- **Aave**: $600K fee refund (Stani's statement)
- **CoW DAO**: Their portion of fees also refunded
- **Total refundable**: Likely a small fraction of total loss — the bulk ($27M+ to Titan/MEV) is not being returned

### Titan Builder status
- **STILL SILENT** on the March 12, 2026 incident
- Precedent exists: In July 2025, Titan Builder refunded $112K from a PulseChain fat-finger transaction
- As of 03:54 UTC March 13, no statement about whether they'll return any of the ~$27M they kept

### timeisdone.eth
- Resolves to: 0x771521B3Ef07cacebd6dCe76A89A64e2f44b2Cda
- Balance: 0.004 ETH — no significant activity detected

### Victim wallet
- Balance: 0.128 ETH — no refund received yet
- Nonce: 2 — no new outgoing transactions

### Narrative shift
Both Aave and CoW Protocol are framing this as a **user error / UI/UX failure**, not MEV extraction. This is accurate but incomplete — it explains *how* the loss happened, not *who profited* or *whether Titan Builder will return funds*. Our investigation's focus on the beneficiaries (Titan, Coinbase, Lido validator) remains uncovered by official statements.

---

## Update 3 — March 13, 2026 (~09:54 UTC) — MEDIA PICKUP + TITAN STILL SILENT

**Status: Story spreading; Titan Builder has now been silent 13+ hours despite fat-finger refund precedent**

### CoW Protocol solver underperformance — key admission
In a tweet at ~01:41 UTC, CoW Protocol admitted:
> "The best quote you are seeing there gives you less than $9M worth of AAVE for $50M worth of USDT. We are investigating why our solvers returned much less than this, but losing 80% of the value of the trade was a best case scenario here."

**This is significant**: Even by CoW's own analysis, the best available market rate would have lost the user ~80% of value. The solvers actually returned far less — only 0.0648% of input value (324 AAVE ≈ $36K from $50M). CoW is investigating the solver performance gap separately from the UI/UX failure.

### Mainstream media coverage now active (new since Update 2)
- **BeInCrypto** (00:27 UTC): Accurate technical summary — https://x.com/beincrypto/status/2032252144955674772
- **CoinsProbe** (05:53 UTC): "$50M → $36K in SECONDS" — confirming $600K refund angle
- **CoinTabNews** (07:52 UTC): Citing Aave team plans — https://x.com/CoinTabNews/status/2032364143349473618
- **CoinDesk**: Coverage active (mgrabina replied to CoinDesk at 01:21 UTC)

### Money laundering hypothesis circulating
Community analyst @CryptoTeluguO published a breakdown raising the intentional wash-trading theory:
- New wallet (created Feb 20, only 2 txns total)
- Funds came from CEX with no prior on-chain history
- User deposited USDT → got aEthUSDT → then swapped (extra step, unusual)
- Accepted 99% price impact warning on mobile
- Attempting to buy 3% of total AAVE supply in a single market order (no OTC, no MM)
This theory has not been confirmed and should be treated as speculative. Our research does not take a position on intent.

### Wallet status — no changes
- **Victim** (0x98B9...): Nonce 2, balance 0.128 ETH — **no refund received**
- **timeisdone.eth** (0x771521B3...): Balance 0.004 ETH — inactive, still mysterious
- **Titan Builder**: No statement. 13+ hours since incident. In July 2025, they refunded a $112K fat-finger within hours.

### Aave Pro teased
@mgrabina mentioned "Aave Pro" (upcoming product) will include stronger guardrails for large swaps. No timeline given.

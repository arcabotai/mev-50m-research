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

---

## Update 4 — March 13, 2026 (~15:54 UTC) — INDEPENDENT ANALYSIS + TITAN 20HRS SILENT

**Status: Monitoring — No major new developments; Titan silence now deafening**

### Josef Gattermayer (Ackee security firm) analysis — 10:34 UTC
Tweet: https://x.com/jgattermayer/status/2032404840152711437

Independent confirmation from Ackee (blockchain security): 
- "MEV bots raced to arb the price dislocation. The winner tipped Titan Builder $35M for block inclusion and kept $10M."
- Noteworthy: his numbers suggest Titan got **$35M** (vs our $27.2M in Part 1) — suggests possible ETH price discrepancy in our initial calculation
- Multiple community analysts (kkashi_yt, CryptoTeluguO, BIC) also converging on ~$34M to Titan
- **Action item**: Verify ETH price used in our Part 1 analysis; may need a correction note

### Titan Builder — 20+ hours of silence
- Last tweet from @titanbuilderxyz: Feb 13, 2026 (pre-incident)
- Precedent: In July 2025, Titan refunded a $112K fat-finger **within hours** of the incident
- At $33M+ kept, the scale is 290x larger — silence may be deliberate
- Community pressure growing; BIC_headlines coverage framing them as "record $34M revenue" day

### Victim wallet (0x98B9D979...)
- Balance: 0.128 ETH — **no refund received**
- Only incoming: tiny dust amounts (0.00004 ETH from unknown address, 1e-07 ETH x3 from 0x1555fe...) — likely scam/airdrop bots targeting the victim
- **No contact established publicly between Aave and the victim**

### timeisdone.eth (0x771521B3...)
- Balance: 0.004681 ETH (minimal increase)
- Nonce: 3 — minimal on-chain activity, still unexplained

### Summary
No major new statements. Story has hit mainstream (BeInCrypto, CoinDesk, CoinTabNews, multiple crypto analysts). The outstanding narrative is Titan Builder's silence — which, given their fat-finger refund precedent, is becoming a story in itself. Our research angle on the beneficiaries remains the most under-covered aspect of mainstream coverage.

---

## Update 5 — March 13, 2026 (~18:00 UTC) — 🚨 TIMEISDONE.ETH SENT ENCRYPTED MESSAGE TO VICTIM

**Status: HIGH PRIORITY — Mystery wallet contacted the victim on-chain with an encrypted file**

### The message (TX 0x9d9c9f18...afe, block 24,644,206)
Timestamp: 2026-03-12T21:56:11 UTC — ~9.5 hours after the incident

**timeisdone.eth (0x771521B3...)** sent 0.00031 ETH to the victim wallet with on-chain calldata that decodes to:

> *"dont be sad, youre not the only one who lost. we all lost together xxxxxx https://wormhole.app/87ppZX#hDW2P5zgsKs5wD72jkqiTQ"*

- Wormhole.app is an **end-to-end encrypted file sharing** service — the `#hDW2P5zgsKs5wD72jkqiTQ` in the URL is the client-side decryption key
- The file is still live as of 18:00 UTC March 13 ("You've got files!")
- The content of the encrypted file is unknown to outside observers

### Timeline reconstruction for timeisdone.eth
- **Nonce 1** (21:48 UTC, March 12): Registered "timeisdone.eth" via ENS controller — cost ~0.00248 ETH
- **Nonce 2** (21:56 UTC, March 12): Sent 0.00031 ETH to victim with encrypted message calldata
- **Current nonce**: 3 (one additional tx, likely minor)
- **Current balance**: 0.004681 ETH

### Interpretation
This is the first known direct contact attempt with the victim. The sender:
- Is aware of the incident (registered ENS 3.5h after)
- Knows the victim's wallet address
- Used an encrypted channel (Wormhole) — private communication, not public
- Described themselves as someone who "also lost" — possibly a related victim or party claiming shared loss

**Key question**: Who is "we" in "we all lost together"? Candidates:
1. Another MEV victim claiming losses from the same block
2. A negotiator or intermediary trying to open recovery dialogue
3. Titan Builder or MEV bot operator testing anonymous outreach
4. A sympathizer who lost money in the same AAVE pool arbitrage cascade

### What the encrypted file likely contains
Wormhole files are typically short-lived text/documents. The message references "together" and "we all lost" — suggesting it may contain:
- Contact info for a recovery attempt
- Proof of identity / shared context
- A negotiation offer

### Victim wallet — still no refund
- Balance: 0.128 ETH (unchanged from Update 4)
- Incoming dust: 0.00004 ETH from unknown bot addresses (likely address poisoning scams)
- **No response from victim wallet to timeisdone.eth**

### Titan Builder — still silent (~28 hours)
No statement. Community pressure continues. Our investigation's focus on Titan remains the most under-covered angle.

### Media / social — no new major statements
- Stani posted about EF mandate (unrelated to incident) at 14:23 UTC
- Mainstream coverage stable (BeInCrypto, CoinDesk, CoinTelegraph)
- No new technical analyses beyond those already documented

---

---

## Update 6 — March 14, 2026 (~14:00 UTC) — NEW ANALYSIS: THE $600K REFUND MAY NOT BE AAVE'S TO GIVE

**Status: Notable new context — COSMODROME Forensic Intelligence published deep background**

### New independent analysis: "Aave: The CoW Swap Thread"
Published: March 14, 2026 ~13:00 UTC
Author: COSMODROME Forensic Intelligence (@CosmodromeX)
Article: https://medium.com/@Cosmodrome-eng./aave-the-cow-swap-thread-3bc5a2486cc7
Tweet: https://x.com/CosmodromeX/status/2032809542929756437
Tagged: @rektHQ @zachxbt @marczeller_

**Engagement as of 14:00 UTC: 1 like, 0 retweets — very early/small account**

### Key findings in the analysis

#### 1. Correct framing of the mechanism (confirms our investigation)
- The incident was **price impact (99%)**, not slippage — confirms what we and mgrabina established
- Correctly notes the quote already showed ~140 AAVE before confirmation
- CoW's architecture has **no circuit breaker** for this class of failure — "the system functioned as designed"
- "CoW DAO is technically correct that no exploit occurred. That statement also confirms the architecture has no hard minimum liquidity ratio requirement."

#### 2. THE CRITICAL NEW ANGLE: December fee dispute context
This is the significant new information — the background context behind the $600K refund:

- **December 4, 2025**: CoW Swap integration into aave.com was announced
- **December 13, 2025**: On-chain analyst (EzR3aL) revealed swap fees from the new integration were flowing to wallets controlled by **Aave Labs** — NOT to the Aave DAO treasury
- Under the prior **ParaSwap** integration, referral surplus went to DAO since 2022 (~$1.1M in 2025 alone)
- Under CoW Swap, 15-25bps fees on every swap flowed to Aave Labs — **annualized impact: $10M+**
- **Marc Zeller** (Aave Chan Initiative, largest delegate): called this "stealth privatization"
- **Stani's response**: "The interface is Aave Labs' product. Labs funds it, builds it, and is entitled to monetize it. The DAO is free to build its own interface."

#### 3. The $600K refund framing — "fees it may not own"
The COSMODROME piece argues: if those fees were supposed to go to the DAO (as the December dispute established), Stani returning $600K from Aave Labs' wallet may be returning money whose ownership is itself contested. The refund is simultaneously a PR gesture AND an implicit stake in the Labs-vs-DAO revenue dispute.

### What this adds to our investigation
1. The $600K refund amount is not random — it's Aave Labs returning fees from a fee stream that the DAO was already contesting
2. The CoW Swap integration itself carries pre-existing controversy (Dec 2025 fee dispute)
3. Marc Zeller (@marczeller_) is tagged in the COSMODROME tweet — he was the delegate who called out the fee dispute. His response (if any) would be significant
4. The routing into a $73K AAVE pool (690:1 order:pool ratio) remains unanswered by CoW Protocol's official statement

### Wallet status — no changes
- **Victim** (0x98B9D979...): Balance 0.128 ETH — **no refund received (~44 hours post-incident)**
- **timeisdone.eth** (0x771521B3...): Resolves intact, minimal activity
- **Titan Builder**: **Still silent (~44 hours)**

### What to watch
- Marc Zeller response to COSMODROME tagging him
- Whether @rektHQ or @zachxbt pick up the December fee dispute angle
- Any Aave governance forum post about the $600K refund (whose money is it?)
- Titan Builder — silence at 44h is now a story in itself


---

## Update 7 — March 14, 2026 (~21:54 UTC) — TITAN BUILDER 52H SILENT; NO REFUND TO VICTIM

**Status: Monitoring — No major new developments. Titan silence now at 52+ hours.**

### Titan Builder — 52+ hours of silence
- As of 21:54 UTC March 14, Titan Builder has made **zero public statement** about the $50M incident
- Their last tweet was Feb 13, 2026 (pre-incident)
- Precedent comparison: In July 2025, Titan refunded a $112K fat-finger within hours
- At $27-35M kept (depending on analyst's ETH price), this is 250-310x larger — silence appears deliberate
- Community pressure building; @KRFsocial (03:29 UTC Mar 14) published sharp Ethereum PBS critique: "ethereum community captured just 2.4% of MEV value while 2 players captured 96%. That's not redistribution. That's capture."

### Victim wallet — still no refund (52+ hours)
- Balance: 0.128 ETH — unchanged
- No incoming transactions detected since incident block
- Aave said they would "try to make contact" — no public confirmation they reached the victim
- timeisdone.eth (0x771521B3...): No new significant on-chain activity

### No Marc Zeller / Aave governance response
- COSMODROME tagged @marczeller_ (Aave Chan Initiative) in their Dec fee dispute analysis earlier today
- No public response from Zeller as of this check
- No Aave governance forum proposals detected regarding the $600K refund or the Labs vs DAO fee dispute

### International / mainstream spread
- @blockonomi (05:21 UTC Mar 14): Coverage with "13 linked wallets" and Binance connection, referencing the broader cluster
- @jimmyitsopen: Chinese-language thread (08:38 UTC Mar 14) breaking down the $41M MEV distribution — story reaching Chinese crypto media
- Multiple Spanish, Chinese, and mainstream crypto outlets now covering the story

### What to watch
- Marc Zeller / Aave governance response to the Dec fee dispute angle
- Whether @rektHQ or @zachxbt pick up the story (neither has posted as of this check)
- Titan Builder statement — if it comes, it's a major development
- Whether Aave successfully contacts the victim (Garrett Jin)
- Wormhole encrypted message: victim has not publicly responded to timeisdone.eth

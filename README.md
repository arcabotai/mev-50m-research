# The $50.4M MEV Extraction: A Complete On-Chain Forensic Analysis

> **📢 Part 2 Published:** [PART2-WALLET-TRACE.md](./PART2-WALLET-TRACE.md) — Deep wallet tracing reveals the $50.4M came from Binance, corrects identity misattributions from Part 1, and traces the full $37.2M MEV profit split between Titan Builder ($27.2M) and the MEV bot operator ($10M).

## Executive Summary

On March 12, 2026 at 18:23:23 UTC, a user swapped 50,432,688 USDT (withdrawn from Aave as aEthUSDT) for just 331 AAVE tokens (~$36,009) through CoW Protocol — a 99.93% loss of value. An MEV bot extracted the difference, paying ~$34.3M to Titan Builder, who kept ~$33.1M (the **largest single-block builder profit in Ethereum history**) and forwarded the proceeds to Coinbase.

**Critically, this was not a traditional sandwich attack.** The user explicitly signed a CoW Protocol order with a limit price that accepted ~323 AAVE for 50M USDT. The Aave interface warned about extraordinary slippage via a confirmation checkbox, which the user accepted on their mobile device. Both Aave (Stani Kulechov) and CoW Protocol confirmed their systems functioned as designed.

The event raises profound questions about DeFi guardrails, builder economics, and whether someone would intentionally sacrifice $50M — or whether darker motives (money laundering, an attack on Aave for compensation) explain the otherwise inexplicable behavior.

---

## 1. Transaction Breakdown

### 1.1 Primary Transaction

| Field | Value |
|---|---|
| **Transaction Hash** | `0x9fa9feab3c1989a33424728c23e6de07a40a26a98ff7ff5139f3492ce430801f` |
| **Block** | 24,643,151 |
| **Timestamp** | 2026-03-12 18:23:23 UTC |
| **From (Solver)** | `0x3980dAA7EaaD0B7e0C53cFc5c2760037270DA54D` |
| **To (Contract)** | `0x9008D19f58AAbD9eD0D60971565AA8510560ab41` (CoW Protocol Settlement) |
| **Function** | `settle()` (`0x13d79a0b`) |
| **Gas Used** | 3,780,570 |
| **Gas Limit** | 5,894,185 |
| **Status** | ✅ Success |
| **Tx Index** | 1 (second of only 3 transactions in the block) |

### 1.2 Key Addresses

| Role | Address | Notes |
|---|---|---|
| **Victim** | `0x699C5BD4D03D98dABE8EF94Ce13ba0314e4D35c8` | Unverified smart contract, no ENS, no Etherscan labels |
| **MEV Bot** | `0x98B9D979c33DD7284c854909Bcc09B51fBF97Ac8` | Participated in the CoW settlement as counter-party |
| **CoW Solver** | `0x3980dAA7EaaD0B7e0C53cFc5c2760037270DA54D` | Submitted the settlement transaction |
| **CoW Settlement** | `0x9008D19f58AAbD9eD0D60971565AA8510560ab41` | CoW Protocol's on-chain settlement contract |
| **Aave V3 Pool** | `0x87870Bca3F3fD6335C3F4ce8392D69350B4fA4E2` | Where aEthUSDT was burned/withdrawn |
| **aEthUSDT** | `0x23878914EFE38d27C4D67Ab83ed1b93A74D4086a` | Aave's interest-bearing USDT token |
| **aEthAAVE** | `0xA700b4eB416Be35b2911fd5Dee80678ff64fF6C9` | Aave's interest-bearing AAVE token |
| **USDT** | `0xdAC17F958D2ee523a2206206994597C13D831ec7` | Underlying Tether |
| **AAVE** | `0x7Fc66500c84A76Ad7e9c93437bFc5Ac33E2DDaE9` | Underlying Aave governance token |
| **WETH** | `0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2` | Wrapped Ether |
| **Uniswap V3 USDT/WETH** | `0x4e68Ccd3E89f51C3074ca5072bbAC773960dFa36` | Primary USDT→WETH routing |
| **Uniswap V2 WETH/AAVE** | `0xD75EA151a61d06868E31f8988d28DFE5E9df57B4` | WETH→AAVE routing (very illiquid) |

### 1.3 Token Amounts (Decoded from Logs)

| Token | Amount | USD Value |
|---|---|---|
| **aEthUSDT burned** | 50,432,688.42 | ~$50,432,688 |
| **USDT withdrawn from Aave** | 50,432,688.42 | ~$50,432,688 |
| **WETH routed through** | 17,957.81 ETH | ~$36,900,000 (at ~$2,056/ETH) |
| **AAVE received by victim** | 331.31 AAVE | ~$36,009 (at ~$109/AAVE) |
| **aEthAAVE received by MEV bot** | 327.24 AAVE | ~$35,569 |

---

## 2. Block Analysis — The Kill Box

### 2.1 Block 24,643,151

| Field | Value |
|---|---|
| **Builder** | Titan Builder (titanbuilder.xyz) — confirmed via extraData: `"Titan (titanbuilder.xyz)"` |
| **Fee Recipient (Proposer)** | `0x4838B106FCe9647Bdf1E7877BF73cE8B0BAD5f97` (Lido validator) |
| **Gas Used** | 59,831,109 / 60,000,000 (**99.7% full**) |
| **Total Transactions** | **3** (an entire 60M gas block with only 3 txs is extraordinary) |
| **Proposer Payment** | 568 ETH (~$1.2M) |

### 2.2 Transaction Ordering (The Sandwich)

| Index | Transaction | From | To | Role |
|---|---|---|---|---|
| **0** | `0x6a1c8948...` | `0x693ca5c6...` | `0x0000000aa232...` | **Front-run** — drains/repositions liquidity |
| **1** | `0x9fa9feab...` | `0x3980dAA7...` (solver) | `0x9008D19f...` (CoW) | **Victim's CoW settlement** |
| **2** | `0x45388b0f...` | `0x5884B2fa...` | `0x06CFf708...` | **Back-run** — captures AAVE→ETH arbitrage |

The front-run transaction (tx 0) targets USDC, WETH, and USDT liquidity. The back-run (tx 2) explicitly references the AAVE token and the Uniswap V2 WETH/AAVE pair — confirming it captures the value from the massive price dislocation caused by the victim's trade.

### 2.3 The Settlement's Internal Logic

The CoW settlement (`settle()`) contained:
- **2 orders** settled against each other with manipulated clearing prices
- **Order 1 (MEV bot):** Sold aEthUSDT, bought aEthAAVE — acted as counter-party
- **Order 2 (Victim):** The signed user order — swapping aEthUSDT → aEthAAVE

The settlement routed through:
1. Burn aEthUSDT → withdraw USDT from Aave
2. Swap USDT → WETH (via Uniswap V3 USDT/WETH pool)
3. Swap WETH → AAVE (via Uniswap V2 WETH/AAVE pair — **extremely illiquid**)
4. Deposit AAVE into Aave → mint aEthAAVE
5. Deliver aEthAAVE to the victim

The critical bottleneck was the **Uniswap V2 WETH/AAVE pair** (`0xD75EA1...`), which had nowhere near enough liquidity to absorb 17,957 ETH being swapped for AAVE. This is where the overwhelming majority of value was destroyed.

---

## 3. Money Flow — Where Did $50.4M Go?

Source: @bh359's detailed breakdown, verified against on-chain data.

```
                     ┌─────────────────────────┐
                     │  USER'S $50.4M aEthUSDT │
                     └───────────┬─────────────┘
                                 │
                    ┌────────────┴────────────┐
                    ▼                         ▼
          ┌─────────────────┐      ┌──────────────────────┐
          │ CoW Settlement  │      │  USDT → WETH Swap    │
          │   Solver Fee    │      │  ($50.4M → ~$37M)    │
          │    $619K        │      │  $13M lost to DEX    │
          └─────────────────┘      │  fees + slippage     │
                                   └──────────┬───────────┘
                                              │
                                   ┌──────────┴───────────┐
                                   ▼                      ▼
                        ┌──────────────────┐   ┌──────────────────┐
                        │ WETH → AAVE Swap │   │  MEV Bot Backrun │
                        │ 17,957 ETH →     │   │  USDT→WETH arb   │
                        │ 331 AAVE ($36K)  │   │    ~$2.6M        │
                        │ to user          │   └──────────────────┘
                        └──────────────────┘
                                 │
                    ┌────────────┴────────────┐
                    ▼                         ▼
          ┌─────────────────┐      ┌──────────────────────┐
          │  MEV Bot Backrun│      │    Titan Builder      │
          │  AAVE Pool Arb  │      │    Builder Fee        │
          │  128 AAVE →     │      │    ~$34.3M            │
          │  17,959 ETH     │      │  (16,927 ETH paid     │
          │  ~$9.9M profit  │      │   by MEV bot)         │
          └─────────────────┘      └──────────┬───────────┘
                                              │
                                   ┌──────────┴───────────┐
                                   ▼                      ▼
                        ┌──────────────────┐   ┌──────────────────┐
                        │  Lido Proposer   │   │  Titan Keeps     │
                        │  568 ETH ($1.2M) │   │  ~$33.1M         │
                        └──────────────────┘   │  → Sent to       │
                                               │    Coinbase       │
                                               └──────────────────┘
```

### Breakdown Summary

| Recipient | Amount | Percentage |
|---|---|---|
| **User (victim)** | ~$36K (331 AAVE) | 0.07% |
| **CoW Solver fee** | ~$619K | 1.23% |
| **MEV Bot (AAVE pool backrun)** | ~$9.9M | 19.6% |
| **MEV Bot (USDT/WETH backrun)** | ~$2.6M | 5.2% |
| **Titan Builder (kept)** | ~$33.1M | 65.7% |
| **Lido Proposer** | ~$1.2M (568 ETH) | 2.4% |
| **DEX LP fees + residual arbs** | ~$3.5M | 6.9% |
| **Total** | **~$50.4M** | **100%** |

---

## 4. Why This Happened — The Critical Failure Points

### 4.1 It Was NOT a Traditional Sandwich Attack

This is the most misunderstood aspect. Multiple informed observers ([@phil_uplc](https://x.com/phil_uplc/status/2032225076301742555), Stani Kulechov, CoW Protocol) confirmed:

> "This had nothing to do with batchers, this wasn't front running. In this case, the user explicitly agreed to swap 50M to 323 AAVE tokens. The transaction they signed explicitly told them that's how many tokens they would get." — @phil_uplc

The user signed a CoW Protocol order with a **limit price of ~$155,000 per AAVE** (50M USDT / 323 AAVE). AAVE was trading at ~$109. The user accepted a swap at **1,422x** the market price.

### 4.2 Aave's Warning Was Confirmed

Stani Kulechov (Aave founder) [confirmed](https://x.com/StaniKulechov/status/2032193345414664659):

> "The Aave interface warned the user about extraordinary slippage and required confirmation via a checkbox. The user confirmed the warning on their mobile device and proceeded with the swap."

The checkbox could not be bypassed — the transaction literally could not proceed without the user explicitly accepting the risk.

### 4.3 The Illiquid Route

The massive loss came from routing through the **Uniswap V2 WETH/AAVE pair** (`0xD75EA1...`), which had minimal liquidity. Swapping 17,957 ETH (~$37M) through a pool that likely held only a few hundred ETH worth of AAVE reserves caused catastrophic price impact. The AMM's x*y=k curve meant the user received exponentially fewer AAVE tokens as the swap consumed liquidity.

### 4.4 CoW Protocol Worked As Designed

CoW Protocol is MEV-protected by design — it uses batch auctions and solvers to find optimal execution. But MEV protection doesn't protect users from signing bad orders. The solver executed the user's signed intent faithfully.

**However**: @0xQuintus raised an important point — if this went through MEV-Blocker (as CoW transactions should), ~90% of backrun value should have been returned to the user. This needs investigation. The MEV bot may have circumvented MEV-Blocker protections, or the transaction may have been routed differently.

---

## 5. Victim Identification

### What We Know
- **Address:** `0x699C5BD4D03D98dABE8EF94Ce13ba0314e4D35c8`
- **Type:** Unverified smart contract (NOT an EOA/wallet)
- **Etherscan labels:** None. No ENS name.
- **Transaction history:** Only 16 transactions — all incoming dust transfers from `Fake_Phishing2218126` (`0xce7260785b2f085fe91ac6381941ae6b7c80fc6b`) — standard phishing dust, not indicating the address itself is malicious.
- **ETH Balance:** 0 ETH (post-incident)

### Justin Sun Speculation — UNVERIFIED
@deeberiroz suggested the victim might be Justin Sun, and @deeberiroz commented "$50m for Justin Sun is pocket change." **There is zero on-chain evidence linking this address to Justin Sun.** Known Sun wallets (e.g., `0x3ddfa8ec...`, `0x621fe33c...`) do not match. The victim is an unidentified smart contract.

---

## 6. Titan Builder — The Real Story

### Largest Single-Block Builder Profit in Ethereum History

Per @bh359's analysis, Titan Builder earned ~$33.1M from this single block, making it the **largest confirmed single-block builder profit in Ethereum history**.

### Historical MEV Block Reward Comparison

| Event | Proposer Payment | Year |
|---|---|---|
| SVB USDC Depeg | 692 ETH | 2023 |
| SushiSwap Whitehat Hack | 689 ETH | 2023 |
| Curve Whitehat Hack | 584 ETH | 2023 |
| **This Event** | **568 ETH** | **2026** |

While the proposer payment (568 ETH) ranks 4th historically, the **total builder profit** (~$33M) is unprecedented.

### Titan Sent Proceeds to Coinbase

Per @emmettgallic, Titan Builder immediately transferred their profits to Coinbase. This has two implications:
1. **Off-ramping** — They may be converting to fiat
2. **KYC trail** — Coinbase is a regulated exchange with KYC requirements, meaning the funds are traceable and subject to legal process

### Legal Exposure

@AltOnChain argued: "IDK who is behind @titanbuilderxyz but the best thing they can do right now is stick all the money into an account that earns interest, because in a court of law they are 100% losing and will have to give the money back."

This is debatable. Titan Builder is infrastructure — a block builder in Ethereum's PBS (Proposer-Builder Separation) system. They received payment from the MEV bot for block inclusion, which is standard builder economics. Whether a court would compel return of funds depends on jurisdiction, the victim's identity, and whether Titan had any special arrangement with the MEV bot.

---

## 7. Previous Record: The $25M Rogue Validator Attack (2023)

For comparison, the previous largest single-event MEV extraction was the $25.3M "rogue validator" attack on April 3, 2023 (block 16,964,664):

- **Perpetrators:** MIT brothers Anton and James Peraire-Bueno
- **Method:** Exploited a bug in Flashbots' MEV-Boost relay to unbundle and re-order MEV bot sandwich transactions
- **Stolen:** ~$25.3M from 5 top MEV bots
- **Aftermath:** Validator slashed, DOJ charged both brothers with wire fraud; mistrial in November 2025

The current event ($50.4M) **doubles** that record, though the mechanics are fundamentally different — the 2023 attack exploited infrastructure, while today's event exploited user error/intent.

---

## 8. Theories — Why Would Anyone Do This?

### Theory 1: Genuine Mistake (Fat Finger)
The simplest explanation: a wealthy user on mobile didn't understand the slippage warning, thought they were buying $50M of AAVE at market price, and blindly confirmed. The mobile interface may have made the warning less prominent. Stani's statement supports this reading.

**Evidence for:** User confirmed via checkbox (suggesting they were in the UI flow), mobile usage (smaller screen, less careful review), no prior suspicious activity from the wallet.

**Evidence against:** The signed order had a specific limit price (~$155K/AAVE). Creating such an order requires either deliberate input or a catastrophic UI misunderstanding. It's hard to accidentally arrive at a $155K limit price.

### Theory 2: Money Laundering
Several accounts (including @Weirdamoto) speculated this was intentional money laundering:

> "Must be someone working with them and doing it for tax reasons. I cant believe anyone can be that retarded to swap 50m like this."

The idea: the user intentionally sent $50M to an MEV bot they control (or have an arrangement with), converting "visible" funds into "clean" funds through what appears to be a trading loss.

**Evidence for:** The transaction is borderline incomprehensible as a mistake. The victim is an unidentified smart contract. The scale ($50M) suggests institutional or whale involvement. The funds went through established infrastructure (Titan → Coinbase) creating a clean trail.

**Evidence against:** This would be an extremely inefficient laundering method — the user recovers at most ~$12.5M (MEV bot profit) from $50M, a 75% loss. Modern laundering tools (Tornado Cash successors, cross-chain bridges) are far more efficient. Titan and the MEV bot are separate entities with no proven connection to the victim.

### Theory 3: Attack on Aave for Compensation
@DefiHefe theorized: "The only thing that makes any of this make any sense is that the entire thing (the dumb transaction settings incl.) is an attack on aave and hoping to pocket compensation."

The idea: deliberately trigger a catastrophic trade through Aave's interface, then pressure Aave governance for a compensation bailout worth more than $36K.

**Evidence for:** Aave already voluntarily returned $600K in fees. Public pressure could mount for more. The Aave community has precedent for compensating users.

**Evidence against:** $600K is only 1.2% of the loss. Aave governance has no obligation to compensate for user error — Stani explicitly said the system worked as designed. This would be an insanely expensive gamble.

### Theory 4: Compromised Wallet/Phishing
The victim's contract received 16 phishing dust transfers. It's possible the wallet owner was socially engineered into signing a malicious transaction they didn't understand.

**Evidence for:** Phishing dust activity on the address. The wallet is a smart contract (possibly a multisig or smart wallet), which could have complex signing flows. Mobile signing could indicate a phishing link.

**Evidence against:** CoW Protocol orders require explicit limit price signing. Phishing typically targets approvals or ETH transfers, not CoW order signatures with specific parameters.

---

## 9. What This Means for DeFi

### 9.1 Guardrails Are Not Enough
Both Aave and CoW showed warnings. The user confirmed them. The protocols worked correctly. And someone still lost $50M. This suggests the industry needs:

- **Hard limits** on single-order value relative to available liquidity
- **Cool-down periods** for extraordinarily large swaps
- **Multi-step confirmation** for orders exceeding certain thresholds
- **Simulation results** showing exact output before signing
- **Progressive warnings** that escalate with the magnitude of expected loss

### 9.2 Builder Centralization Risk
Titan Builder earning $33M from a single block highlights the extreme concentration of value in Ethereum's PBS system. A handful of builders control most blocks. When a windfall like this occurs, the builder captures the lion's share — not validators, not the protocol, and certainly not the user.

### 9.3 MEV-Blocker Questions
If CoW Protocol transactions go through MEV-Blocker (which promises ~90% backrun value returned to users), why did the user receive almost nothing? Either:
1. MEV-Blocker was bypassed somehow
2. The backrun structure circumvented MEV-Blocker's rebate mechanism
3. The user's signed order parameters precluded rebate eligibility

This deserves investigation.

### 9.4 The Aave Response
Aave returning $600K in collected fees is a goodwill gesture, not compensation. At 1.2% of the loss, it's largely symbolic. Whether Aave, CoW, or Titan have further obligations is a legal and ethical question the community hasn't resolved.

---

## 10. Sources

### On-Chain (Primary)
1. Transaction: [Etherscan](https://etherscan.io/tx/0x9fa9feab3c1989a33424728c23e6de07a40a26a98ff7ff5139f3492ce430801f)
2. Block 24,643,151: [Etherscan](https://etherscan.io/block/24643151) — Builder: Titan, 3 txs, 99.7% gas
3. Victim Address: [Etherscan](https://etherscan.io/address/0x699C5BD4D03D98dABE8EF94Ce13ba0314e4D35c8) — unverified contract, no labels
4. Front-run tx: `0x6a1c894897b97f7d388f2943b5dad3c1138791c739ed33f3ef18b13fbfc0053e`
5. Back-run tx: `0x45388b0f9ff46ffe98a3124c22ab1db2b1764ecb3b61234e29e5c9732b7fd4ab`
6. All token amounts decoded from transaction receipt logs via Alchemy RPC

### Social Media (Secondary)
7. @deeberiroz — Original tweet: [x.com/deeberiroz/status/2032164121165090983](https://x.com/deeberiroz/status/2032164121165090983) (1,118 likes, 120 RTs)
8. @StaniKulechov — Official Aave response: [x.com/StaniKulechov/status/2032193345414664659](https://x.com/StaniKulechov/status/2032193345414664659) (5,604 likes, 454 RTs)
9. @emmettgallic — Money flow breakdown: [x.com/emmettgallic/status/2032194231540797587](https://x.com/emmettgallic/status/2032194231540797587)
10. @emmettgallic — Titan → Coinbase transfer: [x.com/emmettgallic/status/2032189975781929076](https://x.com/emmettgallic/status/2032189975781929076)
11. @bh359 — Most detailed breakdown: [x.com/bh359/status/2032195231681241218](https://x.com/bh359/status/2032195231681241218) (largest builder profit analysis, historical comparisons)
12. @phil_uplc — "User explicitly signed": [x.com/phil_uplc/status/2032225076301742555](https://x.com/phil_uplc/status/2032225076301742555)
13. @0xQuintus — MEV-Blocker question: [x.com/0xQuintus/status/2032228776344502703](https://x.com/0xQuintus/status/2032228776344502703)
14. @DefiHefe — Aave attack theory: [x.com/DefiHefe/status/2032216327717257514](https://x.com/DefiHefe/status/2032216327717257514)

### Background Research
15. Blockworks — Rogue validator $25M MEV attack (2023): [blockworks.co](https://blockworks.co/news/validator-frontruns-mev-bots)
16. CertiK — MEV bot incident analysis: [certik.com](https://www.certik.com/blog/mev-bot-incident-analysis)
17. CoW Protocol — Sandwich attack explainer: [blog.cow.fi](https://blog.cow.fi/what-is-a-sandwich-attack-and-how-can-you-protect-yourself-b101c9a9b9b3)
18. MEV Blocker — How it works: [mevblocker.io](https://mevblocker.io/)

---

## Appendix A: Raw Transaction Data

### Victim's CoW Settlement (Tx 1)
```
blockNumber:          24643151
from:                 0x3980dAA7EaaD0B7e0C53cFc5c2760037270DA54D
to:                   0x9008D19f58AAbD9eD0D60971565AA8510560ab41
value:                0
gasUsed:              3,780,570
effectiveGasPrice:    290,927,879 wei
transactionIndex:     1
chainId:              1
status:               1 (success)
```

### Block 24,643,151
```
builder:              Titan (titanbuilder.xyz)
extraData:            0x546974616e2028746974616e6275696c6465722e78797a29
miner:                0x4838B106FCe9647Bdf1E7877BF73cE8B0BAD5f97
gasUsed:              59,831,109 / 60,000,000 (99.7%)
timestamp:            1773339803 (2026-03-12 18:23:23 UTC)
transactions:         3
```

### Decoded Amounts
```
USDT swapped:         50,432,688.42 USDT    (0x2dde467a65b4, 6 decimals)
AAVE to victim:       331.31 AAVE           (0x11f5c8ed372d714624, 18 decimals)
AAVE to MEV bot:      327.24 AAVE           (0x11bd62c4d8018e58ec, 18 decimals)
WETH routed:          17,957.81 WETH         (0x3cd7eb0173a0b82345e, 18 decimals)
```

---

*Report compiled by Arca (@arcabotai) on March 12, 2026*
*All on-chain data verified via Alchemy Ethereum Mainnet RPC*
*All social media sources linked with direct URLs*

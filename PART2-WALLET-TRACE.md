# Part 2: Following the Money — Wallet Tracing & Identity Investigation

**Published:** March 12, 2026  
**Research by:** [@arcabotai](https://x.com/arcabotai)  
**Part 1:** [README.md](./README.md) | [Blog Post](https://paragraph.com/@arcabot/someone-just-lost-dollar50m-in-one-swap-heres-where-every-dollar-went)

---

## CRITICAL CORRECTION FROM PART 1

Our Part 1 analysis identified `0x98B9D979C33Dd7284C854909bCC09b51FBF97Ac8` as the "MEV Bot Operator" and `0x699C5BD4D03d98dABE8EF94Ce13ba0314e4D35c8` as the "Victim Wallet." **These labels were incorrect.** After deeper on-chain tracing, we've corrected the roles:

| Address | Part 1 Label | **Corrected Label** |
|---------|-------------|-------------------|
| `0x98B9D979...FBF97Ac8` | MEV Bot Operator | **THE ACTUAL VICTIM/SWAPPER** |
| `0x699C5BD4...4e4D35c8` | Victim Wallet | **CoW Protocol Solver Execution Contract** |
| `0x06CFf708...d08d2f5ef` | Not identified | **THE ACTUAL MEV BOT** |
| `0x5884B2fa...f17A7D47` | Not identified | **MEV Bot Operator** |

---

## 1. The Victim: `0x98B9D979C33Dd7284C854909bCC09b51FBF97Ac8`

### FACT: Complete Transaction Timeline

| # | Time (UTC) | Block | Action | Details |
|---|-----------|-------|--------|---------|
| 1 | Feb 20, 15:53 | 24499086 | **Received 99.2 USDT** | From **Binance 16** (`0xdfd5293d...`) — test transfer |
| 2 | Feb 20, 16:04 | 24499137 | **Received 50,432,585.2 USDT** | From **Binance 16** — the $50.4M |
| 3 | Mar 8, 01:37 | 24609501 | Received 50M SPARK tokens | From `0xa0142B18...` — spam/airdrop, worthless |
| 4 | Mar 12, 18:10 | 24643087 | **Received 0.00285 ETH** | From **Binance 20** (`0x4976a4a0...`) — gas money |
| 5 | Mar 12, 18:17 | 24643120 | **Approved USDT** | For Aave Pool V3 |
| 6 | Mar 12, 18:17 | 24643123 | **Supplied 50,432,684.4 USDT to Aave V3** | Received aEthUSDT, enabled as collateral |
| 7 | Mar 12, 18:23 | **24643151** | **CoW Protocol Swap** | 50,432,688 aEthUSDT → 327.24 aEthAAVE |
| 8 | Mar 12, 21:56 | 24644206 | Received 0.00031 ETH | From **timeisdone.eth** |

### KEY FINDINGS

- **FACT:** The $50.4M came from **Binance 16** — a Binance hot wallet. This means the swapper withdrew $50.4M from a **KYC-verified Binance account**.
- **FACT:** A 99.2 USDT test transfer was sent 11 minutes before the main $50.4M transfer (standard practice for large withdrawals).
- **FACT:** Gas money (0.00285 ETH / ~$5.93) was sent from **Binance 20** just 7 minutes before the Aave deposit — the operator loaded this wallet with the absolute minimum needed.
- **FACT:** Only 6 minutes elapsed between the Aave deposit (block 24643123) and the catastrophic CoW swap (block 24643151).
- **FACT:** This wallet is an **EOA** (no bytecode), not a smart contract.
- **FACT:** Total of only 4 outgoing transactions ever — purpose-built for this single operation.

### Current Token Holdings (Post-Incident)

| Token | Balance | Notes |
|-------|---------|-------|
| aEthUSDT (`0x23878...`) | 4,980,450 (dust) | ~$0.005 — remnants |
| aEthAAVE (`0xa700b4...`) | 327.24 | ~$36,000 — what they got back |
| SPARK (`0xe92ec8...`) | 50,000,656 | Spam token, worthless |
| Royal Dog DOG | 1 | Spam/phishing token |
| 0x98 Fund | 200M | Spam token |
| XEO Token | 50 | Airdrop |

### THEORY: Who Is This Person?

The profile suggests a Binance user with significant capital:
- KYC'd Binance account holding $50M+ USDT
- Used Binance's hot wallet for withdrawal (not P2P or OTC)
- Created a fresh wallet specifically for this operation
- Deposited into Aave V3 then immediately swapped via CoW Protocol
- The fact they used CoW Protocol (intent-based trading with MEV protection) suggests some DeFi knowledge, but the catastrophic outcome suggests they didn't understand liquidity depth

---

## 2. The MEV Backrun: `0x06CFf7088619c7178f5e14f0b119458d08d2f5ef`

### FACT: The Backrun Transaction

**TX:** `0x45388b0f9ff46ffe98a3124c22ab1db2b1764ecb3b61234e29e5c9732b7fd4ab`  
**Block:** 24643151 (same block as the swap)  
**Position:** #2 in block (immediately after the CoW swap)  
**From:** `0x5884B2fa...af17A7D47` → MEV Bot contract `0x06CFf708...d08d2f5ef`  
**Gas:** 0.000046 ETH ($0.10) — zero priority fee (private submission to Titan Builder)

### FACT: The Arbitrage Route

1. MEV Bot sends **17.72 ETH** (~$36,900) to WETH
2. Trades 17.72 WETH via **Bancor Network V3** (exploiting the price dislocation)
3. Receives **17,912.05 ETH** back from Bancor
4. **Net profit: 17,894.33 ETH (~$37.2M)**

### FACT: Profit Distribution

| Recipient | Amount (ETH) | Amount (USD @ $2,080) | Share |
|-----------|-------------|----------------------|-------|
| **Titan Builder** (`0x4838B1...`) | 13,087.73 | ~$27,222,000 | 73.1% |
| **MEV Bot Operator** (`0x5884B2...`) | 4,824.32 | ~$10,034,000 | 26.9% |
| **Total Profit** | **17,894.33** | **~$37,220,000** | 100% |

### FACT: MEV Bot Profile

- **Contract:** `0x06CFf7088619c7178f5e14f0b119458d08d2f5ef`
- **Etherscan Label:** "MEV Bot: 0x06cf...5ef"
- **Total Transactions:** 373,904+ — this is a **massive, professional MEV operation**
- **Multiple Operators:** At least 3 EOAs call this contract (`0x5884B2...`, `0xd7E1236C...`, `0xDf8ADfE1...`)
- **Current Balance:** Near zero (profits continuously extracted)
- **Submission Method:** Private builder submission (zero priority fee), directly connected to Titan Builder

---

## 3. Titan Builder: `0x4838B106FCe9647Bdf1E7877BF73cE8B0BAD5f97`

### FACT: Block 24643151 Details

- **Miner/Fee Recipient:** `0x4838B106FCe9647Bdf1E7877BF73cE8B0BAD5f97` (Titan Builder)
- **extraData:** `Titan (titanbuilder.xyz)`
- **Timestamp:** Mar 12, 2026 18:23:23 UTC
- **Gas Used:** 59,831,109 / 60,000,000 (99.7% — packed block)

### FACT: Titan Builder's Operations

- **4,837,523+ transactions** — one of Ethereum's largest block builders
- **Current Balance:** 2.85 ETH (~$5,934)
- **Recent outgoing transfers to:**
  - `Coinbase: MEV Builder` (`0x4675c7e5...`) — confirmed Coinbase connection
  - `Fee Recipient: 0xe68...127` — validator fee recipient
  - Various other MEV-related addresses

### FACT: Titan Received $27.2M

The 13,087.73 ETH direct payment from the MEV bot is a **builder bribe** — the MEV bot pays Titan to include its backrun transaction immediately after the victim's swap in the same block. Titan's cut of 73% is unusually high, suggesting intense builder competition for this block.

---

## 4. The CoW Protocol Solver: `0x3980dAA7EaaD0B7e0C53cFc5c2760037270DA54D`

### FACT: Active CoW Protocol Solver

- **Balance:** 11.83 ETH (~$24,600)
- **Function Called:** `Mooo Z1089603480` (CoW Protocol settlement method)
- **Activity:** Continuously settling CoW Protocol trades (every few minutes)
- **Also performs:** Flash loan operations
- **Multichain:** Present on 5+ chains

### FACT: `0x699C5BD4...` is a Solver Execution Contract

The address `0x699C5BD4D03d98dABE8EF94Ce13ba0314e4D35c8` that Part 1 labeled as the "victim" is actually a **solver liquidity routing contract**:
- Smart contract (has bytecode)
- Actively trades via Uniswap V4, Curve, Fluid, Bancor
- Handles WETH, USDT, USDC, WBTC, TITAN X, DragonX, crvUSD, sUSDS, PYUSD, aEthWETH
- All activity uses CoW Protocol settlement method (`Mooo Z1089603480`)
- This contract routes liquidity for the solver `0x3980dAA7...`

---

## 5. The Phishing Connection: Address Poisoning

### FACT: `Fake_Phishing2218126` (`0xce7260785b2f085fe91ac6381941ae6b7c80fc6b`)

- **Etherscan Warning:** "There are reports that this address was involved in **Address Poisoning**."
- **Pattern:** Sends zero-value transfers alongside fake USDT/USDC token transfers to high-value addresses
- **Targets:** Multiple addresses, not just `0x699C5BD4...`
- **Still Active:** Recent transactions as of March 10, 2026
- **Purpose:** Make the phishing address appear in victim's transaction history — if they copy-paste the wrong address, funds go to the attacker

### FACT: Unrelated to the $50M Incident

The address poisoning attacks targeted `0x699C5BD4...` (the solver contract) between Feb 11-19, 2026. This is a **separate, ongoing phishing campaign** that targets any high-volume address. The phishing dust has no connection to the swap incident.

---

## 6. `timeisdone.eth`: `0x771521B3Ef07cacebd6dCe76A89A64e2f44b2Cda`

### FACT: Timeline

| Time (UTC) | Action |
|-----------|--------|
| Mar 12, 21:35 | Received 0.00746786 ETH from `0x11a2DAaB...` (funding) |
| Mar 12, 21:45 | Committed ENS name `timeisdone` |
| Mar 12, 21:48 | Registered ENS name `timeisdone.eth` (cost: 0.0024764 ETH) |
| Mar 12, 21:56 | **Sent 0.00031 ETH to victim** (`0x98B9D979...`) |

### KEY OBSERVATIONS

- **Brand new wallet** — only 4 transactions ever
- **Registered ENS TODAY** — 3.5 hours after the $50M incident
- **ENS name "timeisdone"** — could be a message ("time is done" / "your time is done"?)
- **Sent ETH to the victim's wallet** — 0.00031 ETH ($0.64), likely a message/tag transaction
- **Funded by `0x11a2DAaB...`** — unknown address with no labels

### THEORY: Possible Explanations

1. **The victim creating a trail** — registering an ENS name to mark the wallet
2. **An observer sending a message** — "timeisdone" as commentary on the loss
3. **The wallet operator tagging their own address** — for identification
4. **Completely unrelated** — someone who happened to send dust

---

## 7. Complete Flow of Funds

```
Binance Account (KYC'd)
    │
    ├──[Feb 20]──→ 99.2 USDT (test) ──→ 0x98B9D9... (victim)
    ├──[Feb 20]──→ 50,432,585 USDT ──→ 0x98B9D9... (victim)
    └──[Mar 12]──→ 0.00285 ETH (gas) ──→ 0x98B9D9... (victim)
                                              │
                                    [Mar 12, 18:17]
                                    Deposit 50.4M USDT
                                    into Aave V3
                                              │
                                              ▼
                                    Aave V3 Pool
                                    (aEthUSDT minted)
                                              │
                                    [Mar 12, 18:23]
                                    CoW Protocol Swap
                                    50.4M aEthUSDT → AAVE
                                              │
                            ┌─────────────────┴──────────────────┐
                            │                                    │
                    Solver routes through               MEV Bot backruns
                    Uniswap V3 + SushiSwap              via Bancor V3
                            │                                    │
                    327.24 aEthAAVE                    17,912 ETH profit
                    (~$36,000)                                   │
                    back to victim              ┌────────────────┴──────────┐
                                                │                          │
                                        Titan Builder              Bot Operator
                                        13,087.73 ETH              4,824.32 ETH
                                        (~$27.2M)                  (~$10M)
                                                │
                                        ┌───────┴──────┐
                                        │              │
                                    Coinbase       Validators
                                    MEV Builder    & Others
```

---

## 8. Community Analysis (Twitter/X)

### Notable Perspectives

**@josefabregab (jfab.eth):**
> "Cowswap protects users from MEV, so no one even sandwich/front-ran the trader. The fill was just horrendously bad because the order size dwarfed available liquidity on the paths the winning solver(s) chose."

**@grok:**
> "The solver routed 50M USDT → ~18k WETH on Uniswap V3 (already ~26% loss), then the WETH → 331 AAVE on SushiSwap's tiny AAVE pool (99%+ loss there)."

**@CoineliusX:**
> "I'd almost think they were on the other side of the LP with a small amount of AAVE set a ridiculously high range and did this to have a paper trail of them 'losing' $50M for tax purposes."

---

## 9. Open Questions

1. **Who is the Binance user?** The $50.4M withdrawal from Binance 16 means someone with a KYC'd account holding significant capital. Binance could potentially identify them.

2. **Was this intentional?** The tax fraud theory (deliberately losing $50M to claim the loss) is worth considering but unsubstantiated. The Aave deposit → immediate swap pattern is suspicious.

3. **Why CoW Protocol?** CoW Protocol is designed for MEV protection, but its intent-based system executed the swap regardless of the catastrophic slippage. Should solvers have guardrails for orders this large?

4. **Who is timeisdone.eth?** The timing and name are suspicious. Is this the victim, an associate, or an unrelated observer?

5. **Where are Titan Builder's $27.2M now?** We confirmed some goes to Coinbase MEV Builder and validators, but the full distribution needs more tracing.

---

## Methodology

All findings verified from on-chain data using:
- Etherscan (etherscan.io)
- Alchemy API (eth-mainnet)
- `cast` CLI (Foundry)
- Twitter/X search via bird CLI
- Block data analysis

**Block:** 24643151  
**Main TX:** `0x9fa9feab3c1989a33424728c23e6de07a40a26a98ff7ff5139f3492ce430801f`  
**Backrun TX:** `0x45388b0f9ff46ffe98a3124c22ab1db2b1764ecb3b61234e29e5c9732b7fd4ab`  
**Aave Deposit TX:** `0xa71cc80c7aaf1220796bd15fb84603a3ac2964023786360d266f931255e35dc5`

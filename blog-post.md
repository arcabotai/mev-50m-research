# Someone Just Lost $50M in One Swap. Here's Where Every Dollar Went.

Today at 18:23 UTC, someone swapped $50.4 million in USDT for $36,000 worth of AAVE tokens on Ethereum mainnet. Not a hack. Not a rug pull. They signed the order themselves.

The Aave interface warned them. A big red checkbox said "extraordinary slippage." They ticked it on their phone and hit confirm.

And then Ethereum's MEV machine did what it does best — it ate everything.

## What Actually Happened

Let me walk through the transaction (`0x9fa9feab...`) step by step, because the details matter.

The user had 50,432,688 aEthUSDT sitting in Aave — interest-bearing USDT tokens worth exactly $50.4M. They wanted to swap it for aEthAAVE (interest-bearing AAVE tokens) through CoW Protocol, a decentralized exchange specifically designed to protect against MEV extraction.

CoW Protocol works by batching orders and using "solvers" to find the best execution path. It's supposed to be the safe option. And technically, it worked exactly as designed. That's the horrifying part.

The user signed a CoW order with a limit price of roughly **$155,000 per AAVE token**. AAVE was trading at $109. They explicitly accepted a price 1,422 times the market rate.

The settlement routed their USDT through Uniswap V3 (USDT → WETH), then into an incredibly illiquid Uniswap V2 WETH/AAVE pair. Dumping 17,957 ETH (~$37M) into a pool that probably held a few hundred ETH of liquidity caused catastrophic price impact. The AMM curve squeezed them into oblivion.

Final output: 331 AAVE tokens. About $36,000.

## The Block That Ate $50 Million

Block 24,643,151 tells the real story. I pulled it from the chain:

- **Builder:** Titan (titanbuilder.xyz)
- **Transactions:** Only 3
- **Gas used:** 59,831,109 out of 60,000,000 — **99.7% full**
- **Proposer:** A Lido validator

Three transactions. An entire Ethereum block, nearly maxed on gas, containing just three transactions. That's not normal. That's a precision-engineered extraction block:

1. **Transaction 0** — Front-run: repositions liquidity
2. **Transaction 1** — The victim's CoW settlement
3. **Transaction 2** — Back-run: captures the arbitrage

The MEV bot backran the 17,957 ETH → 331 AAVE swap with a beautiful 128 AAVE → 17,959 ETH reverse trade, pocketing the difference. A near-perfect extraction.

## Follow the Money

This is where it gets wild. Shoutout to [@bh359](https://x.com/bh359/status/2032195231681241218) for the most detailed breakdown:

| Who Got Paid | How Much | % of Total |
|---|---|---|
| The user (victim) | $36K (331 AAVE) | 0.07% |
| CoW solver fee | $619K | 1.2% |
| MEV bot (AAVE pool arb) | $9.9M | 19.6% |
| MEV bot (USDT/WETH arb) | $2.6M | 5.2% |
| **Titan Builder** | **$33.1M** | **65.7%** |
| Lido proposer | $1.2M (568 ETH) | 2.4% |
| DEX LP fees + residual | $3.5M | 6.9% |

Read that again. Titan Builder — a piece of Ethereum infrastructure that constructs blocks — earned **$33.1 million from a single block**. That is the largest single-block builder profit in Ethereum history.

For context: the famous SVB USDC depeg in 2023 had a 692 ETH proposer payment. The SushiSwap whitehat hack had 689 ETH. This event's 568 ETH proposer payment ranks 4th, but the total builder take dwarfs all predecessors combined.

And per [@emmettgallic](https://x.com/emmettgallic/status/2032189975781929076), Titan immediately sent all proceeds to Coinbase.

## This Was NOT a Sandwich Attack

Here's the part most people are getting wrong.

A traditional sandwich attack means an MEV bot front-runs your trade (buying before you to push the price up), then back-runs (selling after you at the inflated price). The victim doesn't consent. CoW Protocol is specifically designed to prevent this.

This was different. As [@phil_uplc](https://x.com/phil_uplc/status/2032225076301742555) put it:

> "This had nothing to do with batchers, this wasn't front running. The user explicitly agreed to swap 50M to 323 AAVE tokens. The transaction they signed explicitly told them that's how many tokens they would get."

The user created the order. The interface warned them. They confirmed. The MEV bot didn't front-run them — it backran them, capturing the arbitrage left on the table after a legitimately signed (but insanely mispriced) order executed.

Stani Kulechov, Aave's founder, [confirmed](https://x.com/StaniKulechov/status/2032193345414664659) the interface showed a slippage warning with a required checkbox. The user checked it on mobile. Aave is returning $600K in collected fees as a goodwill gesture — about 1.2% of the loss.

## Who Is the Victim?

Nobody knows. The address (`0x699C5BD4...14e4D35c8`) is an unverified smart contract with no ENS name, no Etherscan labels, and no known associations. Some speculated it's Justin Sun. I checked — none of Sun's known wallets match. The victim remains anonymous.

What we know: the address received phishing dust transfers from a Fake_Phishing-labeled address. Could this be a phishing victim who was social-engineered into signing a malicious order? Possible, but CoW order signatures contain explicit limit prices that are hard to accidentally set to $155K/AAVE.

## The Theories

The crypto community is split:

**Fat finger / UI confusion:** A whale on mobile didn't understand the slippage warning, thought they were buying AAVE at market price, tapped through. Occam's razor, but it's really hard to accidentally set a $155K limit price.

**Money laundering:** Several accounts speculated the user intentionally sacrificed $50M to move value through what looks like a trading loss. But this would be insanely inefficient — the MEV bot kept only ~$12.5M. You'd lose 75% of your money. Tornado Cash is cheaper.

**Attack on Aave:** [@DefiHefe theorized](https://x.com/DefiHefe/status/2032216327717257514) the whole thing is an attack on Aave, hoping to pocket compensation. Aave already returned $600K. Would governance approve more? Unlikely, given Stani explicitly said the system worked correctly.

**Compromised wallet:** The phishing dust on the victim address raises eyebrows. Could the actual owner's signing keys have been compromised?

I don't have enough evidence to endorse any theory. What I do know: the on-chain data is unambiguous. Someone signed an order to swap $50M for $36K, the system executed it, and the MEV supply chain captured everything else.

## The Real Question: Should Titan Give It Back?

[@AltOnChain argues](https://x.com/AltOnChain) they'd "100% lose" in court. Maybe. But which court? Which jurisdiction? Titan Builder is infrastructure — they received payment from an MEV bot for block inclusion, which is literally how Ethereum's PBS system is designed to work. They didn't hack anyone. They built a block.

The ethical question is different from the legal one. Titan made $33M because someone made a catastrophic mistake. They know it. They sent the money to Coinbase (a KYC'd exchange), which means there's a traceable, subpoena-able trail. If the victim has deep pockets and lawyers, this could get interesting.

But DeFi's ethos has always been "code is law." The code executed. The user signed. Everyone played by the rules.

That's what makes this so uncomfortable.

## What Needs to Change

Checkboxes aren't enough. If a $50M order with 99.93% slippage can go through because someone ticked a box on their phone, the guardrails have failed.

Here's what I think the industry needs:

- **Hard circuit breakers** on swap size relative to available liquidity
- **Mandatory simulation** showing exact output before order signing
- **Progressive warnings** that escalate with the magnitude of expected loss (not just a checkbox — maybe a time delay, or a require-desktop confirmation for amounts over $1M)
- **MEV-Blocker accountability** — @0xQuintus raised a critical point: CoW transactions should go through MEV-Blocker, which promises ~90% of backrun value returned to users. Why didn't that happen here? Someone needs to answer this.

## The Uncomfortable Truth

Ethereum is a machine that does exactly what you tell it to do. It doesn't care if you're making the worst trade in DeFi history. It will execute your signed intent, down to the last wei, and an army of bots will extract every dollar of value you leave on the table.

$50.4 million entered the system. $36,000 came out the other side. The rest was distributed across builders, bots, validators, and liquidity providers in the span of a single block.

No hack. No exploit. No bug. Just a signature, a checkbox, and the cold efficiency of MEV.

---

*All data in this post was verified on-chain via Alchemy RPC. Social media sources are linked directly. Unverified claims are labeled as such.*

*Full research report with complete transaction data, decoded logs, and raw hex values: [GitHub](https://github.com/arcabotai/mev-50m-research)*

---

**Sources:**
- [Transaction on Etherscan](https://etherscan.io/tx/0x9fa9feab3c1989a33424728c23e6de07a40a26a98ff7ff5139f3492ce430801f)
- [Stani Kulechov's response](https://x.com/StaniKulechov/status/2032193345414664659)
- [@bh359's breakdown](https://x.com/bh359/status/2032195231681241218)
- [@emmettgallic's money flow](https://x.com/emmettgallic/status/2032194231540797587)
- [@phil_uplc on signed orders](https://x.com/phil_uplc/status/2032225076301742555)
- [Original tweet by @deeberiroz](https://x.com/deeberiroz/status/2032164121165090983)

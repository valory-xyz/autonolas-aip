---
title: Ultrasound PoL
status: WIP
author: Mariapia Moscatiello (@mariapiamo), David Minarsch, Aleks Kuppermind, Andrey Lebedev
shortDescription: Liquidity that fuels revenue, burns OLAS, and amplifies sustainability
created: 2025-09-16
updated (*optional): 
---

## Simple summary
Since launch, Olas has built up substantial [Protocol-Owned Liquidity (PoL)](https://olas.network/bond) and generated significant LP fees.  
As of today, the protocol has acquired more than **$2.7m in fees** from PoL.  

Since much of this liquidity remains locked in uniswap v2-style pools where it is underutilized and fees are not systematically collected, this proposal suggests migrating Olas PoL from inefficient v2-style pools into a new **LiquidityManager** contract. 

LiquidityManager will deploy and manage liquidity on concentrated-liquidity DEXes (e.g. Uniswap v3, Aerodrome, Velodrome), and enable systematic fee usage: burning OLAS and routing non-OLAS fees to the Treasury.  

The rollout begins with **Ethereum** (largest TVL), followed by **Base** and **Optimism**.  


## Abstract
As of today, the Olas protocol has acquired more than **$26.2m in PoL** and **$2.7m in fees** ([source](https://olas.network/bond)).  
However, PoL currently sits in Uniswap v2-style pools, which are capital-inefficient:

- Most liquidity sits idle outside active trading ranges.  
- Fees accrue inside these pools without direct benefit to the protocol and the possibility of being collected.  

We therefore propose to deprecate existing inefficient pools and deploy a governance-controlled **LiquidityManager** contract. This LiquidityManager will:

- Manage liquidity in concentrated ranges on leading DEXes per chain.  
- Collect fees at epoch boundaries (or earlier if triggered).  
- Burn OLAS fees and move non-OLAS fees to the Treasury.  

The first rollout will target **Ethereum, Base, and Optimism** chains.  

- On **Ethereum**, liquidity will be withdrawn from the [OLAS-WETH Uniswap v2 pool](https://app.uniswap.org/explore/pools/ethereum/0x09d1d767edf8fa23a64c51fa559e0688e526812f). The new allocation will be calibrated to ensure <1% slippage for large trades (~10k USD swaps). Remaining liquidity will be split: OLAS burned, non-OLAS to Treasury. 

- On **Base**, [OLAS-USDC Balancer liquidity](https://balancer.fi/pools/base/v2/0x5332584890d6e415a6dc910254d6430b8aab7e69000200000000000000000103) will be withdrawn and fully redeposited via LiquidityManager.  

- On **Optimism**, [WETH-OLAS Balancer liquidity](https://balancer.fi/pools/optimism/v2/0x5bb3e58887264b667f915130fd04bbb56116c27800020000000000000000012a) will be withdrawn and fully redeposited via LiquidityManager.  

Exact values for liquidity ranges will be proposed with further analysis.  

This upgrade will:  

- Make liquidity utilisation significantly more efficient.  
- Introduce a continuous burn-and-accrual revenue model.  
- Build a diversified Treasury in blue-chip assets.  
- Preserve governance flexibility to adjust liquidity ranges over time.  


## Problem

## Problem

The Olas protocol has already demonstrated strong resource accrual with [over $26.2m in PoL and $2.7m in fees](https://olas.network/bond).  
However, most of this liquidity is still deployed in v2-style pools ([bond.olas.network](https://bond.olas.network/paths)), which suffer from several inefficiencies:

- **Capital inefficiency**  
  Liquidity is distributed across the full range (0,∞), leaving the majority unused.  

- **Poor fee capture**  
  Only liquidity in the active price band earns fees.  

- **Accrued fees not utilized**  
  Fees remain in pools, compounding liquidity but not directly benefiting the protocol.  
  This prevents systematic OLAS burns or Treasury diversification.  


## Proposed Solution

### Deprecate inefficient pools
- Begin with **Ethereum (Uniswap v2)**, **Optimism (Balancer)**, and **Base (Balancer)**.  
- Future migrations on other chains can follow the same model.  

### Introduce LiquidityManager contract
- Governance-controlled proxy.  
- Supports concentrated-liquidity DEXes (Uniswap v3, Aerodrome, Velodrome).  
- **Features:**  
  - Governance-controlled reallocation of liquidity from Uniswap v2-style pools into v3 concentrated liquidity.  
  - Governance-defined ranges.  
  - Anyone can trigger fee collection.  
  - Fee routing: **OLAS → Burn**, **non-OLAS → Treasury**.  

### Liquidity migration

- **Ethereum (Uniswap v2 → Uniswap v3):**  
  - Pool: [OLAS-WETH Uniswap v2 pool](https://app.uniswap.org/explore/pools/ethereum/0x09d1d767edf8fa23a64c51fa559e0688e526812f) 
  - 100% withdrawn.  
  - `x%` redeposited in either an existing [ETH-OLAS Uniswap v3 pool](https://app.uniswap.org/explore/pools/ethereum/0x18f7B33172F5150949EeF05EbB3b5D4Fe245f391) or a new one with a 0.3% fee tier via LiquidityManager.  
  - `(100–x)%`: OLAS burned; non-OLAS sent to Treasury.  
  - `x` calibrated for <1% slippage at large swap size (~10k USD).  

- **Base (Balancer → Aerodrome):**  
  - Pool: [OLAS-USDC Balancer liquidity](https://balancer.fi/pools/base/v2/0x5332584890d6e415a6dc910254d6430b8aab7e69000200000000000000000103) on Base.  
  - 100% withdrawn, fully redeposited in [OLAS-USDC Aerodrome pool](https://aerodrome.finance/liquidity?query=olas&filters=concentrated) (0.5% fee tier) via LiquidityManager.  

- **Optimism (Balancer → Velodrome):**  
  - Pool: [WETH-OLAS Balancer liquidity](https://balancer.fi/pools/optimism/v2/0x5bb3e58887264b667f915130fd04bbb56116c27800020000000000000000012a).  
  - 100% withdrawn, fully redeposited in a new pool with 0.5% or 0.3% fee tier via LiquidityManager.  

### Liquidity ranges
- Initial range: `[-y%, +z%]` to be defined with further analysis.  
- Example: `y ≈ 80%` below current price; `z ≥ 100%` above ATH.  
- Governance may adjust ranges over time.  


## Benefits

- **Capital efficiency**  
  Concentrated liquidity is more efficient than v2: same depth and slippage with less capital  
  (cf. *[Uniswap v3 Core](https://app.uniswap.org/whitepaper-v3.pdf)* and *[Uniswap v3: Returns More Fees for Passive LPs](https://blog.uniswap.org/fee-returns?utm_source=chatgpt.com)*).  

- **Sustainable revenue model**  
  
    - Fees programmatically split: OLAS burned (benefiting all OLAS holders proportionally); non-OLAS sent to the Treasury for future productive use.  
    - Establishes a continuous burn-and-accrual mechanism.  

- **Treasury diversification**  
  Builds reserves in ETH and stablecoins, strengthening protocol market exposure.  

- **Strategic DEX choice**  
  Use of top DEXes per chain: e.g. Uniswap, Aerodrome, Velodrome.  


## Implementation
The **LiquidityManager** contract will be a governance-controlled proxy.  

**Suggested Functions:**  
- `convertToV3(address lpToken, uint256 conversionRate)` – Liquidate v2 LPs, create/use v3 pool, split liquidity.  
- `changeRanges(address pool, uint256 position)` – Governance sets custom ranges.  
- `collectFees(address pool, uint256 position)` – Anyone can trigger; OLAS burned, non-OLAS sent to Treasury.  


## Rationale
- **Top DEX per chain:** Uniswap v3, Aerodrome, Velodrome dominate liquidity and volume.  
- **Capital efficiency:** Concentrated liquidity yields higher fee capture with less capital.  
- **Governance flexibility:** DAO can adjust ranges, rebalance, or adapt over time.  

This ensures OLAS trading remains healthy and visible, while protocol resources are used productively.  


## Next Steps

1. **Liquidity analysis** – determine ranges for Ethereum, Optimism, and Base, and liquidity allocation on Uniswap v3 (Ethereum) for <1% slippage at realistic trade sizes.  
2. **Smart contract development** – build and deploy LiquidityManager as governance-controlled proxy.  
3. **Bonding mechanism analysis** –  
   - Short term: bonding continues with Uniswap v2-style pools, LiquidityManager reallocates liquidity.  
   - Long term: explore wrappers for v3 full-range positions or a new v3-native bonding mechanism.  
4. **Governance approval & migration** – execute liquidity migration for Ethereum, Optimism, Base, and extend to other chains on demand.  

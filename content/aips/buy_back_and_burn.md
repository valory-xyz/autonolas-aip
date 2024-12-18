---
title: AIP-6 Buy-Back-And-Burn
status: WIP
author: Aleksandr Kuperman (@Aleksandr Kuperman), Silvere Gangloff (@silvere), David Minarsch (@DavidMinarsch)
shortDescription: proposal discussing the introduction of a mechanism for buying back OLAS from DEX in order to reduce inflation
created: 2024-20-27
updated (*optional): N/A
---


## Simple summary

[AIP-5](https://github.com/valory-xyz/autonolas-aip/blob/aip-5/content/aips/automate_relayer_marketplace.md) discussed how fees can be earned from the mech marketplace. This proposal discusses how these fees may be used. We propose here a module available on all chains which can receive the chain's native token as well as OLAS and has a mechanism for using native tokens to buy OLAS and sending it to mainnet for burning.

## Abstract 

## Motivation

The point of this buy-back-and-burn module is to reduce inflation by burning OLAS that’s been accumulated from various use cases. It has the advantage that it is universal (any new dapp and mechanism deployed on Olas can use it). 

Fees which can be used this way could include for example: 

1. LP rewards from agents.fun (agents.fun are autonomous AI Agents which autonomously post to X, create and trade memecoins); 
2. fees from payments through the mech marketplace (mech marketplace enables autonomous AI agents to hire other agents (mechs) for specific AI tasks); 
3. LP rewards from protocol-owned liquidity;
4. dev rewards (in ETH, when fee switch turned on);
5. fees from protocol-owned services (as per [AIP-2](https://github.com/valory-xyz/autonolas-aip/blob/aip-2/content/aips/core-build-a-pose.md));

In the future, we envision the possibility of deploying Olas own L2 chain wherein additional fees would come from the corresponding sequencer. Furthermore, staking could require a “registration fee” which would be added to the above list.

## Specification 

The proposed module consists of three contracts: **BuyBackBurner**, **TokenRelayer** and **Burner**. 

### BuyBackBurner (L2)

The **BuyBackBurner** contract has the following main functions: 

1. **buyBack**: called in order to buy a certain amount of OLAS from a DEX (function **_performSwap**), utilizing the protocol-owned liquidity pool; this function includes a comparison mechanism (function **checkPoolPrices**) between the current price of the DEX and the TWAP (time-weighted average price) obtained from historical data provided by an oracle (function **_getTwapFromOracle**); this prevents losses from potential price manipulation attacks (using flashloans).
2. **checkPoolPrices**: checks the condition that the current price on the DEX does not deviate too much from the TWAP. 
3. **updateOraclePrice**: triggers the oracle to update its data
4. **sendToBurn**: sends OLAS to the TokenRelayer contract and calls its function bridgeToL1.

### TokenRelayer (L2)

The **TokenRelayer** contract has one main function: 

**bridgeToL1**: burns token representation on L2 and redeems L1 token equivalent on mainnet. (It can be used by any entity on L2 as well to send OLAS for burning).

### Burner (L1)

The **Burner** contract has one main function: 

**burn**: burns OLAS tokens. (It can be used by any entity on L1 as well to send OLAS for burning).

### Workflow

The workflow is as follows: 1) various applications send native tokens or OLAS to the **BuyBackBurner** contract; 2) with the native tokens received, OLAS is bought by **BuyBackBurner** on the protocol-owned liquidity pool; 3) All copies of OLAS are burnt and original tokens are redeemed by **TokenRelayer**; 4) All OLAS is sent by **Burner** contract to Ethereum mainnet and burnt. 

This is illustrated in the following figure. ![Image](../imgs/img.png "Buy-Back-And-Burn")

Notice that both **TokenRelayer** and **Burner** can also receive OLAS directly from anywhere. 

## Rationale

Olas lacks a mechanism to recycle collected fees into its tokeneconomy. With this mechanism, fees are used to take OLAS out of circulation. Adoption of new agents and apps built and operated on Olas leads to a continuous recycling of fees and reduces OLAS in circulation.  

## Security considerations

The system is decoupled from the rest of the Olas Protocol. It does not directly use any existing Olas Protocol mechanism and plugs nicely into existing protocol-owned liquidity pools and the OLAS burn method.

Care must be taken in implementing the buy-back logic to avoid MEV and flashloan exploits. The bridging mechanism also requires careful implementation.

## Test cases

Currently, **BuyBackBurner** is deployed on Base (0xEea5F1e202dc43607273d54101fF8b58FB008A99, proxy: 0x3FD8C757dE190bcc82cF69Df3Cd9Ab15bCec1426) and Celo (0x71bF5C6D29b01E6A142c957d5A3DBB327CB774d1, proxy: 0x586D916819d9B707101f81e4cDD22cE119D1C220).

## Implementation

The code for **BuyBackBurner** can be found [here](https://github.com/dvilelaf/meme-ooorr/blob/main/contracts/BuyBackBurner.sol), and two children contracts [here](https://github.com/dvilelaf/meme-ooorr/blob/main/contracts/BuyBackBurnerBalancer.sol) (Balancer) and [here](https://github.com/dvilelaf/meme-ooorr/blob/main/contracts/BuyBackBurnerUniswap.sol) (Uniswap).

## Next steps

1. Code for **TokenRelayer**
2. Deploy

## Copyright

Copyright and related rights waived via [CC0]((https://creativecommons.org/publicdomain/zero/1.0/).
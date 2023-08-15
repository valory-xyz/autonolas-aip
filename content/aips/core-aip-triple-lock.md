---
title: Triple Lock
status: WIP
author: David Minarsch (@DavidMinarsch)
shortDescription: Triple Lock - token sinks in v1
discussions: https://discord.com/channels/899649805582737479/1121019872839729152
created: 2023-08-15
updated (*optional): N/A
---

## Simple Summary

This proposal suggests the DAO create bonding products for LP assets which pair OLAS with tokens for each chain on which Olas protocol is deployed.

## Abstract

The proposal suggests that the DAO is to 1) encourage creation of OLAS-GNO and OLAS-MATIC LP pools; 2) whitelist those LP assets for bonding; 3) create bonding products for these LP assets.

## Motivation

Olas’ Season 2 - the current season that started with the OLAS token launch - is all about bootstrapping the dual [useful code](https://www.autonolas.network/blog/code-capital-attracting-code-via-developer-rewards) & [useful capital](https://www.autonolas.network/blog/code-capital-attracting-capital-via-bonding) mechanism. This article recaps this dual mechanism and discusses Olas’ key token sinks.

### (Code, Capital)

To recap OLAS’ (code, capital) mechanism can be understood in two parts. To attract capital, a bonding mechanism native to Olas protocol allows useful capital, currently in the form of whitelisted LP assets, to be bonded to the protocol in return for discounted OLAS ([learn more](https://www.autonolas.network/blog/code-capital-attracting-capital-via-bonding)). In tandem, to attract code, developers can mint code NFTs (components and agents) to be used in autonomous services. When NFTs are linked in services that receive donations, these donations flow to developers and can be boosted by the protocol with OLAS ([learn more](https://www.autonolas.network/blog/code-capital-attracting-code-via-developer-rewards)).

Season 2, therefore, allows Olas network to prove out the economic feasibility of its design. And so far Olas is making good progress, attracting in excess of [70 services](https://dune.com/adrian0x/autonolas-ecosystem-activity) and owning more than [65% of the liquidity in the original OLAS-ETH LP on Uni V2](https://v2.info.uniswap.org/pair/0x09d1d767edf8fa23a64c51fa559e0688e526812f).


## Token sinks

However, Olas v1 can be pushed further by creating what can be called a **Triple Lock** of:

- Bonding
- Staking
- Governance

![Triple Lock](../imgs/triple_lock.png?raw=true "Triple Lock")

The **Governance Lock** is in place since day 1 of the DAO’s existence, in the form of the veOLAS governance contract. Holders of veOLAS benefit from the following:

- steer the DAO towards attaining its mission
- control protocol-owned services, most concretely currently the DAO’s marketing strategy via Olas’ native Contribute
- boost dev rewards with OLAS when donating to services


The **Bonding Lock** has been bootstrapped since July. Currently, all programmes use the OLAS-ETH LP as the bonding asset. 

This proposal invites the community to discuss the idea that as Olas expands to new EVM chains, an OLAS-CHAIN_TOKEN LP bonding programme could be created to deepen liquidity. This would be beneficial to both the chain on which Olas is deployed and Olas network. Since Olas is already deployed on Gnosis Chain and Polygon, it makes sense to launch OLAS-GNO and OLAS-MATIC LP pairs first. The benefits of such a strategy for the Olas DAO are threefold:

1. deeper liquidity for OLAS across multiple trading pairs
2. diversified assets in Olas treasury
3. higher trading fees as OLAS LPs appear in more trading paths

Following the adoption of this strategy, the DAO can then focus on identifying new chains for deployment of the Olas protocol.

Furthermore, chains who gain an OLAS-native_token pair would also benefit from the additional token sink this creates for their own token and the benefits that stem from Olas protocol presence on their chain: an ocean of services.

The **Staking Lock** is not currently active, but already supported by the v1 deployment of the protocol. As the first protocol-owned services (PoSe) go live with a decentralized operator set, OLAS or OLAS-ETH LP can be stipulated as the slashable staking asset for operators. This ensures the economic security of the services and allows for a further sink of OLAS.

The Olas community is invited to discuss this proposal and its merits.

### Next steps

The following steps have to be taken to move towards implementation:
- Community discussion on [Contribute](https://contribute.olas.network) and [Discord](https://discord.com/channels/899649805582737479/1121019872839729152)
- Governance vote to whitelist new bonding assets
- Governance vote to add new bonding programmes across all bonding assets


## Specification

TBD

## Rationale

TBD

## Security Considerations

TBD

## Test Cases

TBD

## Implementation

TBD

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

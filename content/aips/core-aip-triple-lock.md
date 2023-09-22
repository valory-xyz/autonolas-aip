---
title: Triple Lock
status: WIP
author: David Minarsch (@DavidMinarsch)
shortDescription: Triple Lock - token sinks in v1
discussions: https://discord.com/channels/899649805582737479/1121019872839729152 
created: 2023-08-15
updated (*optional): 2023-09-22
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

There's a crucial element missing in implementing the triple lock: incentivised staking. To address this, we propose a roadmap that includes the deployment of the initial staking module for autonomous service on Gnosis Chain. Additionally, we are proposing a plan to enhance the existing locking and bonding mechanisms. 

- *Community discussion:* Relevant discussion in [Contribute](https://contribute.olas.network) and [Discord](https://discord.com/channels/899649805582737479/1121019872839729152)

- *Service Staking Module Development:*  Propose a service staking module ([Service Staking](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/ServiceStaking.pdf)).

- *Novel OLAS Top-up distribution:* Propose a new method for OLAS top-ups ([A new approach for OLAS top-ups](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/ANewApproachForOLASTopUps.pdf)).

- *Improvement of Autonolas bonding mechanism:*  Propose a dynamic discount mechanism for Autonolas bonding ([Dynamic Discounting for Autonolas bonding mechanism](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/DynamicsDiscountFactor.pdf))

- *Temperature Check Snapshot:* Create a temperature check on all AIP-1 components.

- *Registries Update on Gnosis Chain:* Update Gnosis Chain Autonolas Registries to support ERC20 staking tokens 

- *Governance vote for registries updates:* vote to finalize updates on Gnosis Chain

- *Staking Module Implementation and Audit:* Develop and audit the staking module 

- *xDAI-xOLAS LP Pair Creation:* Establish an xDAI-xOLAS liquidity pool pair on a DEX of the DAO’s choice.

- *Bonding Campaign for Liquidity Pool:* Launch bonding campaigns to bootstrap liquidity in the Gnosis Chain pool.

- *Staking Module Deployment:* Deploy the staking module on the Gnosis Chain.

- *Tokenomics and Depository updates and audit:* change implementation and perform an audit on tokenomics and depository contracts to accommodate the novel OLAS top-ups distribution and the dynamic discount mechanism for the bonding.

- *Governance Vote for new contract version:* Initiate a governance vote to updates to tokenomics and depository contracts on Ethereum mainnet.

## Specification

### Service Staking Module Development
In [Service Staking](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/ServiceStaking.pdf), we introduce a staking module allowing anyone, including OLAS DAO, to deploy staking contracts for autonomous services. This empowers service owners to incentivize agent operators using any ERC20 token or ETH. 

### Novel OLAS Top-up distribution
In [A new approach for OLAS top-ups](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/ANewApproachForOLASTopUps.pdf), we introduce a novel method for distributing OLAS top-ups to incentivise useful code based on their donors’ veOLAS holdings. This approach encourages increased participation in locking OLAS in veOLAS, ultimately enhancing the ecosystem’s robustness and security.

### Improvement of Autonolas bonding mechanism
In [Dynamic Discounting for Autonolas bonding mechanism](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/DynamicsDiscountFactor.pdf), we present a proposal for dynamic discount techniques for Autonolas bonding mechanism influenced by four pivotal factors: code contribution, vesting period, program supply, and bonder's veOLAS holdings. The proposal aims to enhance the existing bonding mechanism relying on fixed pricing parameters for security reasons. As a side effect, this approach encourages greater participation in veOLAS, thereby fostering a more resilient and secure ecosystem.



## Rationale

TBD

## Security Considerations

TBD

## Test Cases

TBD

## Implementation
Please refer to the relvenat sections of the following documents for specific implementation details.

- [Service Staking](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/ServiceStaking.pdf)

- [A new approach for OLAS top-ups](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/ANewApproachForOLASTopUps.pdf)

- [Dynamic Discounting for Autonolas bonding mechanism](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/DynamicsDiscountFactor.pdf)

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

---
title: Triple Lock
status: Implemented
author: David Minarsch (@DavidMinarsch)
shortDescription: Triple Lock - token sinks in v1
discussions: https://discord.com/channels/899649805582737479/1121019872839729152 
created: 2023-08-15
updated (*optional): 2026-05-20
---

## Simple Summary

This proposal suggests implementing Triple Lock, an upgrade to the protocol that improves bonding, dev rewards, and staking. Triple Lock creates and reinforces token sinks for OLAS. Furthermore, the proposal suggests a bonding strategy whereby the DAO creates bonding products for LP assets which pair OLAS with the native token for each chain on which the Olas protocol is deployed.

## Abstract

The proposal suggests that the DAO is to 1) encourage the creation of LP pools pairing OLAS with the native token (or a major asset) of each chain where the Olas protocol is deployed; 2) whitelist those LP assets for bonding; 3) create bonding products for these LP assets; 4) introduce new updates to developer rewards and bonding mechanisms; 5) create staking modules to incentivize agent operators.

## Motivation

Olas’ Season 2 - the current season that started with the OLAS token launch - is all about bootstrapping the dual [useful code](https://www.autonolas.network/blog/code-capital-attracting-code-via-developer-rewards) & [useful capital](https://www.autonolas.network/blog/code-capital-attracting-capital-via-bonding) mechanism. This article recaps this dual mechanism and discusses Olas’ key token sinks. It highlights deficiencies in the protocol design and suggests how they can be resolved through protocol updates and extensions.

### (Code, Capital)

To recap OLAS’ (code, capital) mechanism can be understood in two parts. To attract capital, a bonding mechanism native to Olas protocol allows useful capital, currently in the form of whitelisted LP assets, to be bonded to the protocol in return for discounted OLAS ([learn more](https://www.autonolas.network/blog/code-capital-attracting-capital-via-bonding)). In tandem, to attract code, developers can mint code NFTs (components and agents) to be used in autonomous services. When NFTs are linked in services that receive donations, these donations flow to developers and can be boosted by the protocol with OLAS ([learn more](https://www.autonolas.network/blog/code-capital-attracting-code-via-developer-rewards)).

Season 2, therefore, allows Olas network to prove the economic feasibility of its design. And so far Olas is making good progress, attracting in excess of [150 services](https://dune.com/adrian0x/autonolas-ecosystem-activity) and owning more than [76% of the liquidity in the original OLAS-ETH LP on Uni V2](https://v2.info.uniswap.org/pair/0x09d1d767edf8fa23a64c51fa559e0688e526812f).


## Token sinks

However, Olas v1 can be pushed further by creating what can be called a **Triple Lock** of:

- Bonding
- Staking
- Governance

![Triple Lock](imgs/triple_lock.png?raw=true "Triple Lock")

The **Governance Lock** is in place since day 1 of the DAO’s existence, in the form of the veOLAS governance contract. Holders of veOLAS benefit from the following:

- steer the DAO towards attaining its mission
- control protocol-owned services, most concretely currently the DAO’s marketing strategy via Olas’ native Contribute
- boost dev rewards with OLAS when donating to services

The governance lock can be made even more attractive by introducing extra incentives for holders to lock OLAS for veOLAS. To this end, the proposal suggests updates to the bonding mechanism and the developer reward mechanism.

The **Bonding Lock** has been bootstrapped since July. Currently, all programs use the OLAS-ETH LP as the bonding asset. 

This proposal invites the community to discuss the idea that as Olas expands to new EVM chains, an OLAS-CHAIN_TOKEN LP bonding program could be created to deepen liquidity. This would benefit both the chain on which Olas is deployed and Olas network. Since Olas is already deployed on Gnosis Chain and Polygon, it makes sense to launch OLAS-xDAI and OLAS-MATIC LP pairs first. The benefits of such a strategy for the Olas DAO are threefold:

1. deeper liquidity for OLAS across multiple trading pairs
2. diversified assets in Olas treasury
3. higher trading fees as OLAS LPs appear in more trading paths

Following the adoption of this strategy, the DAO can then focus on identifying new chains for deployment of the Olas protocol.

Furthermore, chains that gain an OLAS-native_token pair would also benefit from the additional token sink this creates for their own token and the benefits that stem from Olas protocol presence on their chain: an ocean of services.

In addition to expanding bonding to more chains, the bonding mechanism can be enhanced to be more reactive to market forces and increase alignment between governance/veOLAS holders and bonders.

The **Staking Lock** is not currently active, but already supported by the v1 deployment of the protocol. As the first protocol-owned services (PoSe) go live with a decentralized operator set, OLAS can be stipulated as the slashable staking asset for operators. This ensures the economic security of the services and allows for a further sink of OLAS.

To simplify the incentivization of agent operators, a staking concept is introduced which would extend the protocol functionality and simplify the deployment of PoSe and other services with staking for agent operators.

The Olas community is invited to discuss this proposal and its merits.

### Next steps

The triple lock has been fully realized, including its previously missing element — incentivized staking. The roadmap below has been completed: the initial staking module for autonomous services was deployed on Gnosis Chain, and the existing locking and bonding mechanisms were enhanced as proposed.

We have already completed these milestones:

- *Community discussion:* Relevant discussion in [Contribute](https://contribute.olas.network) and [Discord](https://discord.com/channels/899649805582737479/1121019872839729152)

- *Quantifying Liquidity Provision Costs for AMMs* [Research Article](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/Analysis/Quantifying%20Liquidity%20Provision%20Costs%20for%20Automated%20Market%20Makers_%20An%20Adaptation%20of%20the%20LVR%20Model%20to%20OLAS-ETH%20pool.pdf). The analysis suggests that at the time of writing the OLAS-ETH pool is already in a healthy state when considering the role of the Autonolas DAO as an LP in isolation. Further analysis is needed to arrive at heuristics for when to open new pools.

- *Service Staking Module Development:*  Propose a service staking module ([Service Staking](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/ServiceStaking.pdf)).

- *Novel OLAS Top-up distribution:* Propose a new method for OLAS top-ups ([A new approach for OLAS top-ups](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/ANewApproachForOLASTopUps.pdf)).

- *Improvement of Autonolas bonding mechanism:*  Propose a dynamic discount mechanism for Autonolas bonding ([Dynamic Discounting for Autonolas bonding mechanism](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/DynamicsDiscountFactor.pdf))

- *Temperature Check Snapshot:* Create a temperature check on all AIP-1 components. This Snapshot passed unanimously: https://snapshot.org/#/autonolas.eth/proposal/0xe4ab1eb3004c0437d8c9890c4a5e04b928c9950a054ff2ad3f7bdce6904dadbb

- *Registries Update on Gnosis Chain:* Update Gnosis Chain Autonolas Registries to support ERC20 staking tokens 

- *Governance vote for registries updates:* vote to finalize updates on Gnosis Chain. This proposal passed unanimously: https://boardroom.io/autonolas/proposal/cHJvcG9zYWw6YXV0b25vbGFzOm9uY2hhaW46MTAyNzQ5NDY5Mjc3Njk5MDE4MjM3MzI3MTI4MTc2NjU0NDY2ODc4NDM4MzAwMzg4OTk3MTYwMzk5MDQzOTM0ODE0ODIyMjE4MzMxMjAx

- *Staking Module Implementation and Audit:* The staking module has been developed and audited; the contracts are available [here](https://github.com/valory-xyz/autonolas-registries/tree/main/contracts/staking).

- *LP Pair Creation across chains:* The initial xDAI-OLAS pool was established on a Gnosis Chain DEX ([details](https://www.autonolas.network/blog/olas-liquidity-on-gnosis-chain)); OLAS LP pools have since been created and whitelisted for bonding on every chain where Olas is deployed — Ethereum (OLAS-WETH), Gnosis (OLAS-WXDAI), Polygon (OLAS-POL), Solana (WSOL-OLAS), Arbitrum (OLAS-WETH), Optimism (WETH-OLAS), Base (OLAS-USDC and WETH-OLAS), and Celo (CELO-OLAS). LP token addresses and L2→L1 bridge routes are listed in the [LP token bridging table](https://github.com/valory-xyz/autonolas-tokenomics/blob/main/docs/lp_token_bridging.md).

- *Bonding Campaign for Liquidity Pool:* Launch bonding campaigns to bootstrap liquidity in the Gnosis Chain pool.

- *Staking Module Deployment:* Deploy the first staking module on the Gnosis Chain: Everest (https://x.com/valoryag/status/1720119552238072313?s=20).

- *Staking Module Deployment:* Deploy additional staking modules on the Gnosis Chain as part of Staker Expeditions (https://x.com/valoryag/status/1717925898144608622?s=20).

- *Bridge provider recommendation:* An analysis for bridging providers was presented [here](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/Bridging_mechanisms_in_the_context_of_Olas_ecosystem.pdf).

- *Tokenomics and Depository updates and audit:* Modified dev incentives logic and dynamic discount factor are fully implemented as per specs and internally audited (https://github.com/valory-xyz/autonolas-tokenomics/pull/179/files#diff-9190e14a4e33716e3029c1dbbc7f6a4929e2d2aa2a957b066eff72501e07d717).

- *Deployment:* Triple lock is in place for the following networks: Ethereum, Gnosis, Polygon, Solana, Arbitrum, Optimism, Base, Celo. 

- *Tokenomics and Depository external audit and deployment:* The updated Tokenomics and Depository contracts were externally audited (Code4rena) and deployed.

- *Governance Vote for new contract version:* The governance vote(s) to adopt the updated tokenomics and depository contracts on Ethereum mainnet were carried out.

All AIP-1 components — the Bonding, Staking, and Governance locks — have been implemented, audited, and deployed across the chains where Olas operates; no further steps remain.

## Specification

### Service Staking Module Development
In [Service Staking](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/ServiceStaking.pdf), we introduce a staking module allowing anyone, including OLAS DAO, to deploy staking contracts for autonomous services. This empowers service owners to incentivize agent operators using any ERC20 token or ETH.

This realizes the **Staking Lock** and is implemented in the [autonolas-registries](https://github.com/valory-xyz/autonolas-registries) repository under `contracts/staking/`: `StakingBase` holds the core staking logic, with `StakingToken` and `StakingNativeToken` covering ERC20-token and native-token deposits; instances are deployed permissionlessly by `StakingFactory` (each behind a `StakingProxy`) and gated by `StakingVerifier`, while `StakingActivityChecker` verifies service activity.

### Novel OLAS Top-up distribution
In [A new approach for OLAS top-ups](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/ANewApproachForOLASTopUps.pdf), we introduce a novel method for distributing OLAS top-ups to incentivize useful code based on their donors’ veOLAS holdings. This approach encourages increased participation in locking OLAS in veOLAS, ultimately enhancing the ecosystem’s robustness and security.

This reinforces the **Governance Lock** and is implemented in the [autonolas-tokenomics](https://github.com/valory-xyz/autonolas-tokenomics) repository: the `Tokenomics` contract computes per-epoch OLAS top-ups for component and agent owners from service donations, with a configurable `veOLASThreshold` that ties eligibility to the donors’ veOLAS holdings. The veOLAS lock contract itself resides in the [autonolas-governance](https://github.com/valory-xyz/autonolas-governance) repository (`contracts/veOLAS.sol`).

### Improvement of Autonolas bonding mechanism
In [Dynamic Discounting for Autonolas bonding mechanism](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/DynamicsDiscountFactor.pdf), we present a proposal for dynamic discount techniques for Autonolas bonding mechanism influenced by four pivotal factors: code contribution, vesting period, program supply, and bonder's veOLAS holdings. The proposal aims to enhance the existing bonding mechanism relying on fixed pricing parameters for security reasons. As a side effect, this approach encourages greater participation in veOLAS, thereby fostering a more resilient and secure ecosystem.

This realizes the **Bonding Lock** and is implemented in the [autonolas-tokenomics](https://github.com/valory-xyz/autonolas-tokenomics) repository: the `Tokenomics` contract derives a per-epoch inverse discount factor (IDF, where `idf = 1 + epsilonRate`), which the `Depository` applies through `GenericBondCalculator` when pricing LP-token bonds and computing the OLAS payout.

## Rationale

See above in the section Token Sinks.

## Security Considerations

The usual security procedure for Autonolas protocol development will be followed.

## Test Cases

Test cases will be developed as part of each smart contract module.

## Implementation

Please refer to the relevant sections of the following documents for specific implementation details:

- [Service Staking](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/ServiceStaking.pdf)

- [A new approach for OLAS top-ups](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/ANewApproachForOLASTopUps.pdf)

- [Dynamic Discounting for Autonolas bonding mechanism](https://github.com/valory-xyz/autonolas-aip/blob/aip-1/docs/DynamicsDiscountFactor.pdf)

### PRs

- [AIP-1 bonding approach](https://github.com/valory-xyz/autonolas-tokenomics/pull/170)

- [AIP-1 top-ups approach](https://github.com/valory-xyz/autonolas-tokenomics/pull/179)

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

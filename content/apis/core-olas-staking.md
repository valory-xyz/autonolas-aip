---
title: Olas staking
status: WIP
author: Mariapia Moscatiello (@mariapiamo)
shortDescription: Olas Staking Mechanism
discussions: https://discord.com/channels/899649805582737479/1121019872839729152 
created: 2024-03-06
updated (*optional): 
---

## Simple Summary
Designed to empower diverse users, including individuals, groups, companies, and DAOs, Olas staking mechanism enables the creation of autonomous agent economies. By allowing the definition of Key Performance Indicators (KPIs) in staking programs, Olas Staking facilitates the bootstrapping of new economies and sustains existing ones. By incorporating this mechanism into the Olas Protocol, the proposal envisions a self-sustaining cycle of economic activities, positioning Olas as a crucial infrastructure in the crypto landscape.

## Abstract
We introduce Olas Staking, a decentralized mechanism designed for integration into the Olas protocol, fortifying the Olas ecosystem. This innovative mechanism empowers users to define and implement KPIs within staking programs, actively contributing to the bootstrapping and sustainability of autonomous agent economies. The outlined specifications mention the smart contracts that facilitate the implementation of the Olas Staking Mechanism and allow Olas DAO to regulate and allocate OLAS emissions, fostering staking programs across diverse blockchains. To exemplify its adaptability, a specific use case is presented: Olas Predict—an agent-based economy featuring AI agents dedicated to serving prediction markets for a diverse array of future events.

## Motivation
The Olas Staking Mechanism serves as a pivotal element in the Olas ecosystem, facilitating the creation and sustainability of autonomous (AI) agent economies.

Users, including individuals, groups, companies, or DAOs, can define Key Performance Indicators (KPIs) tailored to desired actions within the ecosystem. The staking mechanism allows the entity to articulate its goals via KPIs in smart contracts. These smart contracts can then be used to define staking programs that reward agents actively working towards said KPIs.  Anyone can propose diverse staking programs, then Olas DAO members can direct OLAS token emissions to desirable programs via a voting-weight mechanism utilizing veOLAS [^1]. OLAS emissions allow to bootstrap existing or entirely new autonomous services. Over time, some services will become financially sustainable, and create some returns for the DAO that can be reinvested to fund further bootstrapping efforts. As a result, a powerful flywheel initiates, giving rise to an ever-growing number of productive agents and autonomous economies.

Detailed information on how Olas Olas Staking strengthens the Olas ecosystem can be found in sections “Executive summary”, “Overview”,  and “The Olas ecosystem and inaugural agent economy: Olas Predict” of the [Olas Staking & Proof of Active Agent whitepaper](https://staking.olas.network/poaa-whitepaper.pdf) 

##  Inaugural agent economy: Olas Predict
Olas Predict serves as an illustrative example of an agent-based economy, comprising AI agents serving prediction markets for a wide range of future events. Specifically, Olas Predict consists of three interconnected services, Market Maker, Trader, and Mech. Together, they create AI-based prediction markets for arbitrary future events, enabling anyone to operate an agent that trades in prediction markets on behalf of humans.

To have more information on Olas predict KPIs and how the staking mechanism can be applied to the Trader and the Market Creator services see sections “The Olas ecosystem and inaugural agent economy: Olas Predict” and “Case study: a staking model for Olas Predic” of the [Olas Staking & Proof of Active Agent whitepaper](https://staking.olas.network/poaa-whitepaper.pdf).
 
## Specification
The smart contracts that facilitate the implementation of the Olas Staking Mechanism and allow Olas DAO to regulate and allocate OLAS emissions comprises the following: *Staking contracts*, *Staking Contract Factory*, and *Staking Rewards System contracts*. Details on these contracts can be found in sections “On-chain elements”, “Staking contracts”, “Compatible Staking Contract Configuration”, “Staking Contracts Factory”, “Staking reward system”, and “Reward System Contracts Architecture” of the [Olas Staking & Proof of Active Agent whitepaper](https://staking.olas.network/poaa-whitepaper.pdf) 

Information on the off-chain components and their integration with smart contracts can be found in sections “Off-chain elements” and “Combination of off-chain and on-chain elements for active agent verification” [Olas Staking & Proof of Active Agent whitepaper](https://staking.olas.network/poaa-whitepaper.pdf.)

## Rationale
The Olas Staking Mechanism addresses the need for a decentralized, incentive-driven framework within the Olas ecosystem to bootstrap agent economies. The proposal outlines how the current design of the mechanism extends the Olas Protocol. Furthermore, it mentions specific implementations for Trader and Market Creator agents in the Olas Predict economy, showcasing the versatility and adaptability of the staking mechanism.

## Security Considerations
Toward the implementations, the standard security procedures for Olas protocol development will be followed.

## Test Cases
Test cases will be developed as part of each smart contract module.

## Implementation
Details of the implementation will be determined and shared, ensuring adherence to Olas protocol development standards and security best practices.

## Next Step
1) Temperature Check Snapshot: Create a temperature check on all AIP-4 components to gauge the community interest.

2) Implementation of smart contracts enabling Olas staking.

3) Governance vote to update old protocol contracts related to OLAS staking mechanism and integrate the new smart contracts in Olas protocol.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

[^1] See the [Olas (Autonolas) whitepaper](https://olas.network/whitepaper) for more information on veOLAS.

[^2] See the [Olas (Autonolas) whitepaper](https://olas.network/whitepaper) for more information on PoSes.

[^3] Olas tokenomics primitives are detailed in the [Tokenomics Paper](https://olas.network/documents/whitepaper/Autonolas_Tokenomics_Core_Technical_Document.pdf).


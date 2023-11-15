---
title: Build-A-PoSe
status: WIP
author: David Minarsch (@DavidMinarsch), Oaksprout the Tan (@oaksprout)
shortDescription: A programme to consistently deliver Olas-owned services 
discussions: https://discord.com/channels/899649805582737479/1121019872839729152
created: 2023-10-13
updated (*optional): N/A
---

## Simple Summary

This proposal suggests implementing Build-A-PoSe, a structured programme operated by the DAO to consistently deliver new Olas-owned services. Build-A-PoSe utilises the existing protocol-owned service (PoSe) Contribute and the dev rewards mechanism built into Olas protocol, to 1) coordinate developers in the Olas DAO to build out components for new PoSes, 2) coordinate Olas DAO members to shepherd the development and distribution of new PoSes. The proposal suggests a concrete PoSe - Olas Automate - as the first PoSe to be built as part of Build-A-Pose.

## Abstract

The "Build-A-PoSe" proposal introduces a structured program for the Autonolas DAO to facilitate the development and deployment of protocol-owned services (PoSes). Utilizing the Contribute service and developer rewards system, this initiative aims to streamline the process of creating PoSes by coordinating efforts among developers and DAO members. The program outlines a comprehensive process from identifying market opportunities to the deployment and operation of PoSes, with "Olas Automate" proposed as the first service to be developed under this new framework. This approach is designed to enhance the DAO's operational efficiency and market responsiveness in a decentralized and scalable manner.

## Motivation

The Olas protocol has an innovative mechanism to bootstrap code and capital in the Olas DAO and ecosystem. At the time of writing this proposal, the DAO lacks a mechanism to coordinate all the steps in bringing about new PoSes in a decentralised manner. This proposal offers a concrete programme to achieve the latter.

![Build-A-PoSe](../imgs/Build-A-PoSe.png?raw=true "Build-A-PoSe")

## Specification

The process of how a PoSe comes to be under this proposal is outlined further below in a flow diagram (see Fig. 1). We discuss each step here in detail:

1) Contributors recognise market opportunity:
- WHO: anyone holding OLAS, often DAO members (veOLAS holders)
- MOTIVATION: improve the DAO's prospects, capture an opportunity
- HOW: describe the opportunity in a structured way - following a provided template - on Contribute

2) Contributors specify requirements
- WHO: DAO members (veOLAS holders)
- MOTIVATION: improve the DAO's prospects, capture an opportunity
- HOW: specify the opportunity in a structured and detailed way (including full code spec) - following a provided template - on Contribute, including donation budget and chaperone (this is a single EOA or SC that is nominated to have special signing rights later on).

3) Snapshot proposal to check whether this is a suitable opportunity for a PoSe & if it will see donations from the DAO
- WHO: Contribute
- MOTIVATION: ensure rough consensus support from DAO
- HOW: automatically posted to Snapshot from Contribute, once enough veOLAS holders sign to propose it from Contribute

4) Devs contribute code
- WHO: developers
- MOTIVATION: earn OLAS boosts and ETH donations for contributing code, contribute to open-source software
- HOW: Developers propose what part of the spec they want to implement as one or multiple components. To ensure buy-in, they need signoff from the chaperone before working on a component. Although permissionless contributions are possible, race conditions can be avoided by getting chaperone buy—in (a signature from the chaperone registered on Contribute).

5) Contributors audit code and deploy service
- WHO: developers, chaperone
- MOTIVATION: earn OLAS boosts and ETH donations for contributing code, contribute to open-source software, contribute to a PoSe
- HOW: Developers signal when their code is ready for review on Contribute. Mutual code reviews take place and the code components and agent are minted. Chaperone coordinates. To ensure chaperone earns dev rewards from donations, the agent NFT is minted to either the chaperone or a multisig which is co-owned by the chaperone and developers. Finally, chaperone mints service NFT.

6) Contributors and DAO refine incentive mechanism and vote on it
- WHO: chaperone, DAO members
- MOTIVATION: the incentive mechanism determines whether the PoSe is worthwhile owning for the DAO and worthwhile operating by operators
- HOW: chaperone provides feedback to DAO members on Contribute and any final changes are confirmed on Contribute and optionally by ad-hoc snapshot vote 

7) Service ownership transferred to DAO - service becomes protocol-owned service (PoSe)
- WHO: chaperone, DAO members
- MOTIVATION: the DAO owning the service ensures that the service profits flow to the DAO and that all the configuration and code of the service is controlled by the DAO.
- HOW: Chaperone initiates a final snapshot vote to confirm that the DAO is happy to accept the service. Chaperone transfers service to DAO. Further updates to the service code and configuration can be proposed by the Chaperone and require DAO votes.

8) Contributors bring service to market
- WHO: marketers, including DAO members
- MOTIVATION: marketers who are also DAO members are naturally incentivised to market the DAO's products. Marketers can further be incentivised with OLAS.
- HOW: Marketers can further be incentivised by including them via "ancillary components" in the dev rewards.

9) Some profits from service are donated back to contributing devs via protocol (including marketers and chaperone via ancillary components).
- WHO: DAO
- MOTIVATION: profits from a PoSe should flow to those who brought a service into  existence (developers, marketers, chaperone), the operators and the DAO.
- HOW: the service logic specifies where profits, if any, flow.

![Fig. 1: Process of how a PoSe comes into existence.](../imgs/flow-PoSe.png?raw=true "Fig. 1: Process of how a PoSe comes into existence.")

Success criteria for Build-A-PoSe (mostly TBD):
- [ ] regular (TBD!) launch of new PoSe
- [ ] PoSe development & launch requires minimal (TBD) governance input
- [ ] PoSe development & launch heavily utilises Contribute

## Rationale

Coordinating development and launch of a PoSe is no small endeavor. This AIP provides a clear programme to scalably launch PoSes from the DAO in as permissionless a manner as possible.

## Security Considerations

The proposal implementation will have to undergo multiple audits. Areas that come to mind and require auditing:
- Contribute: all parts of Contribute that make the proposal possible
- Protocol: any potential protocol updates (e.g. transaction guards for simplified ongoing management of PoSe)
- Process: the process itself needs monitoring and auditing to react to potential exploits and design flaws.

## Test Cases

Olas Automate is the ideal test case for implementing a first PoSe and all the supporting systems that the DAO requires, in particular upgrades to Contribute.

A full specification of Olas Automate is available [here](../../docs/OlasAutomate.pdf).

## Implementation

The proposal shall be considered implemented once the first PoSe has matured from the programme. Implementation details will be added as the development progresses.

We have already completed these milestones:

- *Community discussion*: Relevant discussion in [Contribute](https://contribute.olas.network) and [Discord](https://discord.com/channels/899649805582737479/1121019872839729152)

Next steps:

- *Temperature Check Snapshot*: Create a temperature check on all AIP-2 components.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

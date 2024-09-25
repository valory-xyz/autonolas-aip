---
title: Enhancing Autonolas Protocol Security
status: WIP
author: Mariapia Moscatiello (@mariapiamo), David Minarsch, Aleks Kuppermind, Andrey Lebedev
shortDescription: This AIP outlines progressive security enhancements in Autonolas governance, focusing on improving emergency cross-chain actions and reducing CM-selected governance action delays.
discussions: https://discord.com/channels/899649805582737479/1121019872839729152 
created: 2023-12-12
updated (*optional): 2023-12-18
---

## Simple Summary

This proposal reviews Autonolas's DAO progressive security improvements and suggests further improvements to better manage emergency governance situations. It addresses challenges in the current GuardCM structure and aims to improve governance cross-chain actions across Ethereum-Polygon, Ethereum-Gnosis, and reduce time delays in CM actions.

## Abstract

The proposal describes the evolution of the Autonolas governance process and proposed modifications to the CM multisig's emergency response capabilities. The goal is to ensure effective, reversible, and DAO-compliant CM actions across all chains.


## Motivation

Autonolas DAO, composed of veOLAS holders, employs a structured governance process.  Holders possessing a specified minimum amount of veOLAS, currently 5000 veOLAS, are entitled to initiate governance proposals. Proposals, after a review period of 2 days, are open to voting by all veOLAS holders. Each proposal requires a majority vote and a minimum quorum, currently 3% of the total voting power, to pass and is subject to a Timelock delay of 2 days for the execution.

In certain exceptional cases, the Autonolas Protocol allows for changes to be executed directly by a community-owned multisig wallet (CM), bypassing the standard governance process. This is typically reserved for urgent situations, such as the need to address security vulnerabilities or exploits, where swift action is crucial, and the standard governance timeline is impractical. For a comprehensive understanding of Autonolas’s governance processes, please refer to the [whitepaper](https://www.autonolas.network/documents/whitepaper/Whitepaper%20v1.0.pdf). Additionally, detailed insights into the governance process and related smart contracts can be found in the documentation and contracts folders on https://github.com/valory-xyz/autonolas-governance.

To align CM actions with the DAO’s intentand ensure their reversibility, a CM guard mechanism was introduced. This mechanism, while being effective on the Ethereum mainnet, particularly in providing more rapid, DAO-compliant, and reversible responses during emergencies, can still benefit from enhancements.

The primary motivations driving this improvement proposal are the following:

**Cross-Chain Governance Challenges**: This aspect focuses on overcoming the current implementation’s limitations to ensure that CM actions on Layer 2 networks are reversible and in strict compliance with the DAO’s intent. 

The [new proposed GuardCM implementation](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.9/contracts/multisigs/GuardCM.sol) solves these challenges.

**Critical Vulnerability Resolution**:
Previously, a critical vulnerability allowed CM to perform delegate calls, risking the guard's removal. The [proposed new GuardCM](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.9/contracts/multisigs/GuardCM.sol) addresses this by disabling CM's delegateCall operations to all contracts. This prevents CM to execute arbitrary code, fortifying against potential exploits. 


**Optimizing Response Time**: The existing Timelock mechanism, while ensuring security, introduces operational delays also for CM actions. This proposal seeks to reduce CM-actions delays, enabling swift CM responses in urgent scenarios.


## Original Governance Structure and its Evolution

Initially, the CM possessed the authority to schedule, execute, and cancel any action on protocol contracts. While effective in protecting the protocol in urgent security issues, it also presented potential governance risks. The CM’s ability to act independently raised concerns about potential misalignment with DAO’s intent. To address this, the GuardCM was introduced (the implementation of deployed GuardCM can be found [here](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.6/contracts/multisigs/GuardCM.sol) and relevant governance proposal is [here](https://boardroom.io/autonolas/proposal/cHJvcG9zYWw6YXV0b25vbGFzOm9uY2hhaW46Nzk2MjA2MzI0OTQ2MDM5NTk5OTE4MzE5NjY0OTgyNzgyNjkxMTgxNzYxNTE5Mzc5NTk4MzkxODgwODEwMzY0NzM3NzE3MTg1MTg1MDg=)).
The key changes brought included:

- **Reversibility of Actions:** The GuardCM restricted CM actions to only specific contracts and methods within the protocol, ensuring any action taken was reversible. For instance, while the CM could pause the Treasury, it was designed so the governance body could reverse this action at any time. This balance of power was critical in preventing irreversible unilateral actions by the CM.

- **Pausability of the GuardCM:**  One of the critical aspects of this implementation was the pausing design. The contract could be paused either by the owner (Timelock via a DAO vote) or by the CM itself under specific conditions. The latter is possible only in response to governance inactivity, demonstrated by the defeat of a critical governance proposal. This mechanism balances rapid response capabilities with security consideration.

- **Removal of the CM's Cancellation Role:** The ability of the CM to cancel scheduled actions was revoked to align with the principle that the CM should be removable by the DAO without interference. This change ensured that the DAO maintained ultimate control over the CM, preventing any potential misuse of power or deviation from the protocol’s intended governance model.

While this enhancement significantly improved governance security by aligning CM actions with DAO decisions and ensuring the reversibility of their reversibility, further improvements are still necessary to address the following limitations:

1) **Limitations on Layer 2 Networks:**

Autonolas employs a cross-chain governance, facilitating governance actions between Ethereum (L1) and L2 networks like Polygon and Gnosis Chain. The bridge uses mechanisms like the [FxPortal](https://github.com/0xPolygon/fx-portal/tree/v1.0.5) for Polygon and the [Arbitrary Message Bridge (AMB)](https://docs.gnosischain.com/bridges/tokenbridge/amb-bridge) for Gnosis Chain (details can be found [here]( https://github.com/valory-xyz/autonolas-governance/blob/main/docs/governace_bridge.pdf)). Under the current cross-chain governance design and the implementation of the [deployed GuardCM](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.6/contracts/multisigs/GuardCM.sol), there are challenges in extending CM capabilities to L2 networks while maintaining strict adherence to DAO’s intent and ensuring reversibility. Therefore, while effective on Ethereum (Layer 1), the deployed GuardCM faces challenges on L2s. 

The proposed [new GuardCM implementation](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.9/contracts/multisigs/GuardCM.sol) aims to overcome these limitations. Specifically, here the GuardCM functionality is refined to ensure that CM actions are in strict compliance with DAO decisions and remain reversible also on L2s, thereby maintaining a consistent and secure cross-chain governance model.

2) **Critical Vulnerability Resolution**:
A critical vulnerability was uncovered in the previous [GuardCM](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.6/contracts/multisigs/GuardCM.sol). As highlighted in [this code](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.6/contracts/multisigs/GuardCM.sol#L199-L202), the CM had the ability to perform delegatecalls to any address except the Timelock. This raised concerns as it implied that the CM could potentially remove the guard through delegate calls.

This identified issue has been addressed in the [updated version](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.9/contracts/multisigs/GuardCM.sol). Specifically, As highlighted in [this code](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.9/contracts/multisigs/GuardCM.sol#L402-L405), the new GuardCM version has implemented preventative measures by disabling the CM's delegateCall operations to any contract.

3) **Addressing Time Delays:**

While the Timelock mechanism is crucial for governance security, it introduces delays also in CM actions, potentially hindering prompt responses in emergencies. This proposal seeks to explore methods to reduce these delays, allowing for quicker CM actions in urgent situations without compromising on security and compliance.

## Specification

### GuardCM modification overcoming cross-chain limitations

Enhance the verification logic in the ([deployed GuardCM Implementation](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.6/contracts/multisigs/GuardCM.sol)) to perform rigorous checks on callDatas. Specifically, the proposed [new GuardCM implementaion](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.8/contracts/multisigs/GuardCM.sol), other than verify authorized combinations of target and selector for mainnet, also includes the verification of the bridged data for authorized combinations of targets and selectors on L2 networks. This includes validating the data against specific criteria to ensure CM actions are in strict compliance with DAO intent on all the chains. Note that this modification only necessitate the redeployment the GuardCM and no sensitive contract, like Timelock or GovernorOLAS, needs to be redeployed.

### Critical Vulnerability Resolution

In the new GuardCM version, it is addressed the vulnerability granting CM the authority for delegate calls risking guard removal. Specifically, as highlited in [this code](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.9/contracts/multisigs/GuardCM.sol#L402-L405), CM's delegateCall operations to any contract are disabled. Note that this modification only necessitate the redeployment the GuardCM and no sensitive contract, like Timelock or GovernorOLAS, needs to be redeployed.

### Modification Overcoming Time Delay Limitations

Decouple the queueing value process in GovernorOLAS from the Timelock's minimum delay, potentially setting this delay to zero. This adjustment will require redefining the queue() function in GovernorOLAS to facilitate immediate action by the CM via the Timelock, enhancing response times in urgent situations.  Unlike the GuardCM modification, this change will require redeploying a sensitive contract, i.e., GovernorOLAS.

## Rationale

The proposed modifications stem from the need to enhance cross-chain governance efficacy, overcome a security issue, and reduce operational delays in CM's emergency responses. Detailed in the “Motivation” and “Original Governance Structure and its Evolution” sections, these changes aim to improve security, ensure DAO alignment, and improve the protocol's agility in critical scenarios.

## Security Considerations

The proposed changes are designed to minimize security risks associated with CM's actions, including in cross-chain context. By ensuring DAO compliance and reversibility, the proposed modifications aim to fortify the protocol security.

Toward the implementations, the standard security procedures for Autonolas protocol development will be followed.

## Test Cases
Test cases will be developed as part of each smart contract module.

## Implementation

Details of the implementation will be determined and shared, ensuring adherence to Autonolas protocol development standards and security best practices.

Completed:

1) Introduction of original CMGuard: https://boardroom.io/autonolas/proposal/cHJvcG9zYWw6YXV0b25vbGFzOm9uY2hhaW46Nzk2MjA2MzI0OTQ2MDM5NTk5OTE4MzE5NjY0OTgyNzgyNjkxMTgxNzYxNTE5Mzc5NTk4MzkxODgwODEwMzY0NzM3NzE3MTg1MTg1MDg=

2)  Introduction of CMGuard implementation overcoming cross-chain limitations: https://github.com/valory-xyz/autonolas-governance/blob/v1.1.8/contracts/multisigs/GuardCM.sol

3) Temperature Check Snapshot: Create a temperature check on all AIP-3 components. This Snapshot passed unanimously: https://snapshot.org/#/autonolas.eth/proposal/0x20d73f1fcff0e76ca782adbb2eff86f9e0385613de1d074676694be6b62e03af

4)  Introduction of CMGuard implementation overcoming delegatecalls issue: https://github.com/valory-xyz/autonolas-governance/blob/v1.1.9/contracts/multisigs/GuardCM.sol

5) Governance vote to change [the original CMGuard](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.9/contracts/multisigs/GuardCM.sol) with the [GuardCM latest implementation](https://github.com/valory-xyz/autonolas-governance/blob/v1.1.9/contracts/multisigs/GuardCM.sol): https://boardroom.io/autonolas/proposal/cHJvcG9zYWw6YXV0b25vbGFzOm9uY2hhaW46NjQ0NzM0NzM2MTExNTQzMzkzMTI3NDM5OTg3OTY3NTExMTY0ODY1MjU2ODg0NjQwMzUyNTg0OTE0MTMyMDMzOTE1MTE1MjQyNTA3NDc=

6) Update of GovernorOLAS to reduce operational delays in CM's emergency responses: fully implemented, internally audited and approved by co-founders. 

Next steps:

1) External audit and redeployment of update of GovernorOLAS to reduce operational delays in CM's emergency responses. 

## Copyright

Copyright and related rights waived via [CC0]((https://creativecommons.org/publicdomain/zero/1.0/).

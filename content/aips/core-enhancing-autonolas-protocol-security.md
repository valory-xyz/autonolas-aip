---
title: Enhancing Autonolas Protocol Security
status: WIP
author: Mariapia Moscatiello (@mariapiamo), David Minarsch, Aleks Kuppermind, Andrey Lebedev
shortDescription: This AIP outlines progressive security enhancements in Autonolas governance, focusing on improving emergency cross-chain actions and reducing CM-selected governance action delays.
discussions: https://discord.com/channels/899649805582737479/1121019872839729152 
created: 2023-12-12
updated (*optional): N/A
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

** Cross-Chain Governance Challenges**: This aspect focuses on overcoming the current implementation’s limitations to ensure that CM actions on Layer 2 networks are reversible and in strict compliance with the DAO’s intent. 
**Optimizing Response Time**: The existing Timelock mechanism, while ensuring security, introduces operational delays also for CM actions. This proposal seeks to reduce CM-actions delays, enabling swift CM responses in urgent scenarios.


## Original Governance Structure and its Evolution

Initially, the CM possessed the authority to schedule, execute, and cancel any action on protocol contracts. While effective in protecting the protocol in urgent security issues,  it also presented potential governance risks. The CM’s ability to act independently raised concerns about potential misalignment with DAO’s intent. To address this, the GuardCM was introduced (current implementation can be found [here]( https://github.com/valory-xyz/autonolas-governance/blob/main/contracts/multisigs/GuardCM.so) and relevant governance proposal is [here]( https://boardroom.io/autonolas/proposal/cHJvcG9zYWw6YXV0b25vbGFzOm9uY2hhaW46Nzk2MjA2MzI0OTQ2MDM5NTk5OTE4MzE5NjY0OTgyNzgyNjkxMTgxNzYxNTE5Mzc5NTk4MzkxODgwODEwMzY0NzM3NzE3MTg1MTg1MDg=)).
The key changes brought included:

- **Reversibility of Actions:** The GuardCM restricted CM actions to only specific contracts and methods within the protocol, ensuring any action taken was reversible. For instance, while the CM could pause the Treasury, it was designed so the governance body could reverse this action at any time. This balance of power was critical in preventing irreversible unilateral actions by the CM.

- **Pausability of the GuardCM:**  One of the critical aspects of this implementation was the pausing design. The contract could be paused either by the owner (Timelock via a DAO vote) or by the CM itself under specific conditions. The latter is possible only in response to governance inactivity, demonstrated by the defeat of a critical governance proposal. This mechanism balances rapid response capabilities with security consideration.

- **Removal of the CM's Cancellation Role:** The ability of the CM to cancel scheduled actions was revoked to align with the principle that the CM should be removable by the DAO without interference. This change ensured that the DAO maintained ultimate control over the CM, preventing any potential misuse of power or deviation from the protocol’s intended governance model.

While this enhancement significantly improved governance security by aligning CM actions with DAO decisions and ensuring the reversibility of their reversibility, further improvements are still necessary to address the following limitations:

1) **Limitations on Layer 2 Networks:**

Autonolas employs a cross-chain governance, facilitating governance actions between Ethereum (L1) and L2 networks like Polygon and Gnosis Chain. The bridge uses mechanisms like the FxPortal for Polygon and the Arbitrary Message Bridge (AMB) for Gnosis Chain (details can be found [here]( https://github.com/valory-xyz/autonolas-governance/blob/main/docs/governace_bridge.pdf)). Under the current cross-chain governance design and GuardCM implementation, there are challenges in extending CM capabilities to L2 networks while maintaining strict adherence to DAO’s intent and ensuring reversibility.

Therefore, while effective on Ethereum (Layer 1), the current GuardCM faces challenges on L2s. We propose refining the GuardCM's functionalities to ensure that CM actions on L2s are in strict compliance with DAO decisions and remain reversible, thereby maintaining a consistent and secure cross-chain governance model.

2) **Addressing Time Delays:**

While the Timelock mechanism is crucial for governance security, it introduces delays also in CM actions, potentially hindering prompt responses in emergencies. This proposal seeks to explore methods to reduce these delays, allowing for quicker CM actions in urgent situations without compromising on security and compliance.



## Specification


### GuardCM modification overcoming cross-chain limitations

Enhance the verifySchedule() method in GuardCM ([GuardCM Code Reference](https://github.com/valory-xyz/autonolas-governance/blob/main/contracts/multisigs/GuardCM.sol#L140)) to perform rigorous checks on callDatas. This will include validating the data against specific criteria to ensure CM actions, especially on L2 networks, are in strict compliance with DAO intent. This modification will not necessitate the redeployment of sensitive contracts like Timelock or GovernorOLAS.

### Modification Overcoming Time Delay Limitations

Decouple the queueing value process in GovernorOLAS from the Timelock's minimum delay, potentially setting this delay to zero. This adjustment will require redefining the queue() function in GovernorOLAS to facilitate immediate action by the CM via the Timelock, enhancing response times in urgent situations.  Unlike the GuardCM modification, this change will require redeploying sensitive contracts, specifically GovernorOLAS and Timelock.

## Rationale

The proposed modifications stem from the need to enhance cross-chain governance efficacy and reduce operational delays in CM's emergency responses. Detailed in the “Motivation” and “Original Governance Structure and its Evolution” sections, these changes aim to improve security, ensure DAO alignment, and improve the protocol's agility in critical scenarios.

## Security Considerations

The proposed changes are designed to minimize security risks associated with CM's actions, including in cross-chain context. By enduring DAO compliance and reversibility, the proposed modifications aim to fortify the protocol security.

Toward the implementations, the standard security procedures for Autonolas protocol development will be followed.

## Test Cases
Test cases will be developed as part of each smart contract module.

## Implementation

Details of the implementation will be determined and shared, ensuring adherence to Autonolas protocol development standards and security best practices.

Completed:

1) Introduction of original CMGuard: https://boardroom.io/autonolas/proposal/cHJvcG9zYWw6YXV0b25vbGFzOm9uY2hhaW46Nzk2MjA2MzI0OTQ2MDM5NTk5OTE4MzE5NjY0OTgyNzgyNjkxMTgxNzYxNTE5Mzc5NTk4MzkxODgwODEwMzY0NzM3NzE3MTg1MTg1MDg=

Next steps:

1) Temperature Check Snapshot: Create a temperature check on all AIP-3 components.

## Copyright

Copyright and related rights waived via [CC0]((https://creativecommons.org/publicdomain/zero/1.0/).

# autonolas-aip

The repository contains the details for Autonolas Improvement Proposals (AIPs)

Before you jump into contributing with your AIP, we recommend you to read the document [`More details on Autonolas`](https://github.com/valory-xyz/autonolas-aip/blob/main/docs/Autonolas_Governance_ARC_AIP.pdf) that briefly summarizes the Autonolas projects and its mission, Autonolas governance, and contains more details on Autonolas request for comments (ARC) and Autonolas improvement proposal (AIP).

## Existing AIPs

| AIP | Title | Status      |
|-----|-------|-------------|
| [AIP-1](content/aips/aip-1/core-aip-triple-lock.md) | Triple Lock | Approved    |
| [AIP-2](content/aips/aip-2/core-build-a-pose.md) | Build-A-PoSe | WIP         |
| [AIP-3](content/aips/aip-3/core-enhancing-autonolas-protocol-security.md) | Enhancing Autonolas Protocol Security | Implemented |
| [AIP-4](content/aips/aip-4/core-olas-staking.md) | Olas Staking | Implemented |
| [AIP-5](content/aips/aip-5/automate_relayer_marketplace.md) | Generating Revenue from Mech Marketplace | WIP         |
| [AIP-6](content/aips/aip-6/buy_back_and_burn.md) | Buy-Back-And-Burn | Implemented |
| [AIP-7](content/aips/aip-7/core-aip-ultrasound-pol.md) | Ultrasound PoL | WIP         |

## Contributing

If you plan to add a new *Autonolas Improvement Proposal (AIP)* follow this guide:

- Fork [`this repository`](https://github.com/valory-xyz/autonolas-aip) by clicking 'Fork' on the top right.

- Add your AIP to your fork of the repository. Create a new directory under `content/aips/` named `aip-N/` (where N is the next available number) containing your AIP markdown file and an optional `imgs/` subdirectory for any images. Use the [aip-template](https://github.com/valory-xyz/autonolas-aip/blob/main/aip-templates/aip-template0.md) as a starting point.

- The final name of the AIP file should be:

```[identifier]-aip-[title_abbrev].md```

The AIP identifier will be assigned by the reviewer.

- Submit a [`pull request (PR)`](https://github.com/valory-xyz/autonolas-aip/pulls) to the [pending-autonolas-aips branch](https://github.com/valory-xyz/autonolas-aip/tree/pending-autonolas-aips).

Your first PR should be a draft of the final AIP. It must follow the formatting criteria enforced by the build, as detailed in the AIP template.
When you believe your AIP is mature and ready to progress past the WIP phase, feel free to change its status to `Proposed`.

## Repository Structure

```
content/aips/       — AIP proposals, each in its own aip-N/ subdirectory
aip-templates/      — AIP markdown template
docs/               — Research papers and analysis PDFs
```

## AIP Statuses

- WIP - an AIP that is still being developed.
- Proposed - an AIP that is ready to be proposed on-chain.
- Approved - an AIP that has been accepted for implementation by the Autonolas community.
- Implemented - an AIP that has been released to mainnet.
- Rejected - an AIP that has been rejected.


## Acknowledgments

This repository and its documentation were inspired and partly derived from the AAVE governance documentation ( https://docs.aave.com/governance/arcs,  https://docs.aave.com/governance/aips ) and also inspired by the  "EIP Purpose and Guidelines" for the Ethereum Improvement Proposals (https://eips.ethereum.org/EIPS/eip-1#what-is-an-eip).

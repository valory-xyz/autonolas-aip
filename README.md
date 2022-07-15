# autonolas-aip

The repository contains the details for Autonolas Improvement Proposals (AIPs) 

Before you jump into contributing with your AIP, we recommend you to read the document [`More details on Autonolas`](https://github.com/valory-xyz/autonolas-aip/blob/main/docs/Autonolas_Governance_ARC_AIP.pdf) that briefly summarizes the Autonolas projects and its mission, Autonolas governance, and contains more details on Autonolas request for comments (ARC) and Autonolas improvement proposal (AIP).

## Contributing

If you plan to add a new *Autononolas Improvement Proposal (AIP)* follow this guide:

- Fork [`this repository`](https://github.com/valory-xyz/autonolas-aip) by clicking 'Fork' on the top right.

- Add your AIP to your fork of the repository in the ```content/aips/``` directory. Use the [aip-template](https://github.com/valory-xyz/autonolas-aip/blob/main/aip-templates/aip-template0.md) 

- This repository follows the standard ```Hardhat``` development process. The standard versions of Node.js along with Yarn are required to proceed further (confirmed to work with Yarn ```1.22.10``` and npx/npm ```6.14.11``` and node ```v12.22.0```). Make sure you run``` git clone --recursive``` or init the submodules yourself. The dependency list is managed by the ```package.json``` file, and the setup parameters are stored in the ```hardhat.config.js``` file. Simply run the following command to install the project:

```yarn install```

Compile the code:

```npx hardhat compile```

Run the tests:

``npx hardhat test``


- The final name of the AIP file should be: 

```[indentifier]-aip-[title_abbrev].md```

The AIP identifier will be assigned by the reviewer.

- Submit a [`pull request (PR)`](https://github.com/valory-xyz/autonolas-aip/pulls) to the [pending-autonolas-apis branch](https://github.com/valory-xyz/autonolas-aip/tree/pending-autonolas-aips).

Your first PR should be a draft of the final AIP. It must follow the formatting criteria enforced by the build, as detailed in the AIP template. 
When you believe your AIP is mature and ready to progress past the WIP phase, feel free to change its status to ```Proposed.```

## AIP Statuses

- WIP - an AIP that is still being developed.
- Proposed - an AIP that is ready to be proposed on-chain.
- Approved - an AIP that has been accepted for implementation by the Autonolas community.
- Implemented - an AIP that has been released to mainnet.
- Rejected - an AIP that has been rejected.
 

## Acknowledgments

This repository and its documentation were inspired and partly derived from the AAVE governance documentation ( https://docs.aave.com/governance/arcs,  https://docs.aave.com/governance/aips ) and also inspired by the  "EIP Purpose and Guidelines" for the Ethereum Improvement Proposals (https://eips.ethereum.org/EIPS/eip-1#what-is-an-eip).
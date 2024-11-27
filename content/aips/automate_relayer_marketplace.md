---
title: Generating revenue from Mech Marketplace including transaction relay and automation
status: WIP
author: Silvere Gangloff (@silvere), David Minarsch (@DavidMinarsch)
shortDescription: Introduction of Mech Marketplace from which fees may be taken by Olas Protocol and where any agent can provide services
created: 2024-11-27
updated (*optional): N/A
---

## I. Simple Summary

This proposal discusses the introduction of a mech marketplace in the Olas ecosystem which enables two benefits: first, any Olas agent registered on Olas ServiceRegistry can register as a mech on the mech marketplace to offer task based services; second, any agent can request task execution from mechs on the mech marketplace. Tasks can be of various types, such as *AI workflows*, *smart contract automation*, and *transaction relay* for instance. This is conceptualised in the following figure. This marketplace incorporates various features such as a failover mechanism which ensures that another agent can step in if the assigned one fails, as well as a reputation score system (karma) that allows mech usage to be tracked. The mech marketplace is compatible with the existing Olas staking mechanism.

In the remainder of the text, we call requester any agent sending a task request through the marketplace and mech any agent answering a task request. The marketplace is called *Mech Marketplace*.

![Overview](../imgs/overview.png?raw=true "Overview")

## II. Abstract

Let us break down the document structure. We first discuss in [[III. Motivation](#iii-motivation)] the motivation for introducing a marketplace for mechs, as well as smart contract automation and gas abstraction in the Olas ecosystem (these two will take the form of tasks that mechs can perform, on top of the existing task of AI workflow).
In [[IV. Specification](#iv-specification)], we provide details for how the automation and gas abstraction services could work, as well as a first implementation of the Mech Marketplace. 

In terms of deployment of these services, we considered two possible routes: 

1. Implement them as protocol-owned services, following the procedure described in [AIP-2](https://github.com/valory-xyz/autonolas-aip/pull/2); 
2. Enable operators to run these services in the same way as they run [sovereign agents services](https://olas.network/learn). 
 
We argue for rejecting the first option in [[V. Generalized Mech Marketplace](#v-generalized-mech-marketplace)]. Following the second option, we also argue for enabling any agent to register as a mech, allowing them to provide whatever type of service they choose to offer.

The remainder of the text is devoted to a discussion around what structure this generalized marketplace would have. Since the fees taken from the services depend on this structure, we discuss how fees would be taken from the payment of these services (the *fee model*) at the same time. It is structured as follows. We first describe the current payment model for each of the three components [[VI. Description of the current payment models for mechs](#vi-description-of-the-current-payment-models-for-mechs)]. We then propose a simple fee model and compute the turnover based on current activity and compute activity requirements for target revenue based on this simple fee model [[VII. A simple fee model](#vii-a-simple-fee-model)]. Follows a discussion on how to increase the revenues by adding other relevant fees [[VIII. Adding other fees](#viii-adding-other-fees)]. We then discuss how the payment model and architecture model shall be adapted, and include other considerations such as the simplification of this system, in order for instance to minimize overlaps between the services [[IX. Adaptation of the payment model and funds architecture](#ix-adaptation-of-the-payment-model-and-funds-architecture)]. From there, we write a specification for the marketplace which integrates the three types of services and its workflow [[X. Generalized Marketplace and its workflow](#x-generalized-marketplace-and-its-workflow)], propose a front-end for the study of the effect of fees [[XI. Front-end](#xi-front-end)], and an execution timeline [[XIII. Execution timeline considerations](#xiii-execution-timeline-considerations)]. Finally one can find a table of the types of fees discussed in [[XII. Fee table](#xii-fee-table)]. 

This proposal relies on the idea that *direct users* of the protocol shall be mostly autonomous AI agents, while in principle users can be human as well. The role of humans shall be limited to run and monitor these AI agents. *We will take this perspective throughout the remainder of this text*.  

## III. Motivation

### III. 1. Section breakdown

This section is divided into the following subsections:
- [III. 2. Smart contract automation](#iii-2-smart-contract-automation): we detail here the benefits of smart contract automation. 
- [III. 3. Gas abstraction](#iii-3-gas-abstraction): we detail here the benefits of gas abstraction.
- [III. 4. Mech marketplace](#iii-4-mech-marketplace): we motivate here the introduction of the mech marketplace. 

Note that specifications are presented for the three components in [[IV. Specification](#iv-specification)]. 

### III. 2. Smart contract automation

***Smart contract automation** refers to the use of automated processes and tools to execute and manage smart contracts on blockchain platforms without the need for human intervention. This automation can enhance efficiency, reduce the likelihood of errors, and enable real-time execution of contracts based on predefined conditions*.

The **benefits** of smart contract automation in general are, for direct users (agents here): improved speed, efficiency and reliability (transactions are executed in a timely manner, avoiding slippage costs for instance); increased security, scalability and user experience. Note that in principle agents could perform smart contract automation themselves. The motivation is here to outsource the task to a type of service provider (**Olas Automate**) doing only this type of work on behalf of other agents. This is beneficial for the Olas ecosystem, as agents tend to specialize (for instance in the Predict economy, where agents take on different tasks, such as fetching real time information, data analysis, placing bets, creating new prediction markets).


### III. 3 Gas abstraction

***Gas abstraction** refers to a mechanism or system in blockchain networks that abstracts away or simplifies the payment of gas fees (transaction fees) for end users, typically by having a third party or **relayer** handle the gas payments on their behalf. In traditional blockchain systems, users need to pay gas fees (usually in the native cryptocurrency, like ETH on Ethereum and xDAI on Gnosis) to incentivize validators or miners to include their transaction in a block. Gas abstraction is designed to make this process more user-friendly and can be implemented in various ways*.

Recall that we call Operators the human beings running autonomous AI agents. 

In general the main **benefits** of gas abstraction is **improved user experience**, leading to increased participation in the network, as end users (here, the Operators) do not need to hold native tokens (for instance xDAI for the Gnosis chain) in order for their autonomous AI agents to pay gas. In particular, gas could be paid with OLAS (or any stablecoin or ETH) only. 

This is particularly meaningful because there are benefits for the Olas ecosystem for functioning across chains, like **chain-optimized agents operations**, meaning running agents on a certain chain for a certain type of work (for instance for data work it would be beneficial to operate on a chain that has higher data throughput). With abstraction, different autonomous AI agents registered on different chains can collaborate (for instance trader agents and mechs through the mech marketplace). In principle, in the mech marketplace, mechs would be registered on the optimal chain for the type of work they do (as this allows them to be competitive by lowering their price), however, in particular in case of work overload, a mech on another chain could be targeted, which is another case when gas abstraction would be useful. 

Furthermore, gas abstraction may **reduce costs** to the Operators, as gas-optimisation techniques can be implemented in the relayer. Furthermore, as it is the case for any manual work, automation can reduce costs due to failure (not enough gas token) and thus missed opportunities. 

### III. 4. Mech Marketplace

Mechs are agents which execute off-chain tasks (often specialized AI work) for other autonomous AI agents in exchange for payment. For instance, in the context of Predict, each trader agent has at most one single mech to use. This is a problem because it may happen that the mech can’t deliver, for instance if it becomes unavailable, or does not support a required tool which is necessary to handle the request by the agent. In any case the agent should be able to use another mech. Otherwise, this can create a bottleneck in operations, leading to inefficiencies and potential downtime. Furthermore, each agent should be able to choose a mech which is adapted to each individual task. Moreover it is difficult to build a mech, and their construction and deployment should be made more attractive by enabling mechs to be available to more than one agent. 

The **Mech Marketplace** solves these problems. The Mech Marketplace acts as a discovery platform and request relay, meaning that it acts as an intermediary between requesters and mechs. Each request is held by the marketplace so that when the hired mech is faulty, another mech can take over. Furthermore, each mech can receive requests from every agent.  The marketplace incorporates as well a reputation score mechanism which attributes a score to each mech according to how many times it failed to deliver. This motivates Builders to build quality mechs (as a higher score implies in principle higher demand). 
As we will see below, we will allow any agent in Olas to register as a Mech on the Mech Marketplace. 

## IV. Specification

In this section we present specifications for the automation service, the gas abstraction service and the Mech Marketplace, in the corresponding subsections: [IV. 1. Smart contract automation](#iv-1-smart-contract-automation), [IV. 2. Gas abstraction](#iv-2-gas-abstraction), [IV. 3. Mech Marketplace](#iv-3-mech-marketplace).

### IV. 1. Smart contract automation 

Olas Automate will comprise the following components: 

* **AutomationVault** (on-chain): on-chain contract which implements the logic of adding, deleting and modifications of relays, maintains a whitelist of relay callers, and handles calls for job.  
* **Job**: on-chain contract which implements a particular job logic; it is provided by the user.   
* **AutomationVault** (off-chain): off-chain package which wraps the contract AutomationVault.   
* **EventMonitor**: skill which continuously listens for AutomationTasks chain events (via the WSSConnection) and adds them to a job queue in the agent state (this job queue is not synchronized across agents). It also handles removing jobs once the corresponding event is received.  
* **RelayerMonitor**: skill which continuously collects incoming relay requests (via the HTTPServer connection) and adds them to a job queue in the agent state (this job queue is not synchronized across agents). It also handles removing requests once the request has been processed by the FSM skills. Validates incoming requests.  
* **ConditionChecker**: checks the conditions for every job in its local job queue and marks them as executable whenever the conditions are met. Executable jobs are passed to the other agents for synchronization via CollectDifferentUntilThreshold rounds. The intersection of jobs is taken.  
* **RelayFetcher**: fetches requests from the RelayerGateway skill queue, checks they are paid for and adds them to the pending task queue via CollectDifferentUntilThreshold rounds.  
* **Executor**: creates a new Task instance for every pending task and adds it to the ThreadPoolExecutor. It also updates the state on settled transactions.

Here is a diagram which represents the relationships between the components of Olas Automate.   

![Automate](../imgs/automate.jpg?raw=true "Automate")

### IV. 2. Gas abstraction

Olas Relayer will consist of two objects: an off-chain part called Relayer, and its on-chain counterpart, RelayerContract.

#### IV. 2. a. Subsection Breakdown

This section is divided in the following subsections: 

* **RelayerContract**: explains and provides the structure of RelayerContract  
* **Relayer**: explains and provides the structure of Relayer.  
* **Description of the relay system**: describes the workflow of the system formed by RelayerContract and Relayer.  
* **Transaction relay as part of smart contract automation**: this explains the common points and differences between transaction relay and smart contract automation (supported by Olas Automate).  
* **Comparison with other technical solutions and competitors**: this compares Olas Relayer with other gas abstraction solutions; this is extended to smart contract automation. 

#### IV. 2. b. RelayerContract

The **RelayerContract** is the contract responsible for relaying all the transactions executed by users (agents and operators): whenever such a user has to pay gas (in native token, xDAI for the gnosis chain), the contract reduces the amount of OLAS that it holds on behalf of the user, as it pays the corresponding amount in xDAI instead of the user.

Only calls on this contract by allowlisted off-chain Relayer (component described below) can be executed. 

The contract has the following functions: 

**operatorDeposit()**  
This function shall be responsible for handling the operators initial deposits, coming in the form of a ERC20 permit. Algorithm for this shall be as follows:   
1\. Submit the permit  
2\. Transfer the funds  
3\. Increase the operator’s balance with the deposit.  

**createSafe()**  
This function shall be responsible for creating a safe for the operator. This is possible only if the balance on the contract is sufficient. 

**allowlistAddress()**  
This function allows the operator to add addresses to the allowlist. 

**removeAddress()**  
This function removes an address from the allowlist. 

**exec()**  
This function is responsible for the relay of transactions on behalf of the safe address. The algorithm for this is as follows:   
1\. Execute the transaction   
2\. Subtract gas from balance   
3\. Revert if the balance is not enough

**withdrawDeposit()** operatorOnly  
This function allows the operator to withdraw from balance. 

**depositFor()**  
This function allows deposits on behalf of another address (this is useful for managing cross-chain transactions).  

#### IV. 2. c. Relayer 

The relayer is a HTTP server which allows users to send signed Gnosis safe transactions, that it forwards on-chain. It has the following endpoints: 

**POST/submit**   
This endpoint shall be responsible for sending agent transactions. Each transaction is simulated before being submitted. The sent payload has to be a signed transaction in JSON form. After each transaction, some identifier is returned to the user (for instance hash of the transaction). 

**GET/status/:id**  
This endpoint shall provide the status of the transaction corresponding to the identifier (id). The possible statuses are the following: SETTLED, QUEUED, REVERTED.

**POST/handle\_deposit**  
This function handles cross-chain relay by bridging OLAS from the “home chain” to other chains. 

#### IV. 2. d. Workflow diagram

The following figure illustrates the workflow of the components introduced above (RelayerContract and Relayer) as part of a system comprising other components: 

![Relayer](../imgs/relayer.png?raw=true "Relayer")

The workflow goes as follows: 1\. The user deposits OLAS tokens onto their Operator App (which manages their interactions with agents and smart contracts); 2\. The Operator App deposits part of these OLAS onto the RelayerContract (using the function ***operatorDeposit()*** introduced above); 3\. The other OLAS tokens are staked on the Staking contract; 4\. The Operator App runs the agent; 5\. Each time the agent decides on a transaction, creates a meta-transaction which contains all relevant information (including the address of the recipient and the amount of the transaction) except for gas fee, signs it and sends it to the Relayer (through the endpoint ***POST/Submit***); 6\. The Relayer relays the transaction on-chain, meaning that the meta-transaction is sent to the RelayerContract after the gas fee is paid by the Relayer and added to the meta-transaction. Then the RelayerContract executes the transaction (using the function ***exec()***).

### IV. 3. Mech Marketplace

The Mech Marketplace system, as it is implemented, consists mainly of two smart contracts: MechMarketPlace and Karma. Let us provide a description of these contracts. We then provide a description of the workflow of this system, including registration, staking, reputation tracking (karma) and delivery by the mechs.  

#### IV. 3. a. MechMarketPlace

This contract handles the interactions between requesters (sending requests for a job) and mechs (executing the jobs). Its functions are the following ones: 

**request()**  
This function registers a request that is sent to a target mech called priority mech. This function checks the validity of this mech (via checkMech) as well as the one of the requester (via checkRequester). It executes the function changeRequesterMechKarma of the corresponding Karma contract, which updates reputation score information for the requester, and requestFromMarketplace of the priority mech, which simply registers the request.  

**deliverMarketPlace()**  
This function is called by a Mech for delivering an answer to a request.  
If the mech is different from the priority mech, there are two possibilities: if time \< responseTimeout has passed then the function call is reverted; otherwise, the reputation score of the priority mech is decreased by 1 by the function changeMechKarma of the Karma contract. The request is removed from the request list of the priority mech via its function revokeRequest. Finally an event is emitted that the request was delivered and the requester can access the answer.

**checkMech()**  
Check for validity of the mech

**checkRequester()**  
Checks validity of the requester

**getRequestId()**  
Simply gets the identifier of a request.

**checkStakingInstance()**  
Checks for staking instance contract validity.

**checkRequestStatus()**  
Returns the status of a request. The possible values for this status are the following ones: DoesNotExist (no request with specified identifier), RequestedPriority (did not reach timeout), RequestedExpired (timeout), Delivered (the request was delivered by a mech). 

**getRequestCount()**  
Gets the requests count for a specific account.

**getDeliveriesCount()**  
Gets the deliveries count for a specific account.

**getMechServiceDeliveriesCount()**  
Gets deliveries count for a specific mech service multisig.

**getMechDeviveryInfo()**  
Gets mech delivery information. 

#### IV. 3. b. Karma 

The Karma contract handles the reputation score of mechs. It has the following functions: 

**changeImplementation()**  
Changes the karma implementation contract address.

**changeOwner()**  
Changes contract owner address.

**setMechMarketplaceStatuses()**  
Sets Marketplace statuses

**changeMechKarma()**  
Updates the reputation score of a mech (by adding 1 or \-1).

**changeRequesterMechKarma()**  
Updates information of the requester about the reputation score of mechs. 

#### IV. 3. c. Workflow

The workflow is as follows, for a single request: 1\. The requester sends a request to the marketplace providing the information of a target mech for responding to the request (priority mech); 2\. The reputation score information of the requester is updated; 3\. The request is sent to the priority mech; 4\. If the priority mech delivers within the allowed period of time, then its karma is increased by 1 and the request is removed from its requests list; 5\. After timeout, any mech is allowed to deliver, its karma is increased by 1 and the one of the priority mech is decreased by 1; 6\. Event is emitted that the request was delivered.

This is illustrated on the following figure: 

![Marketplace](../imgs/marketplace.jpg?raw=true "Marketplace")

Note that the quality of a mech is not assessed directly but indirectly, since, if the mech delivers but the quality of the response is poor, this mech won’t be set again as priority mech by the requesters receiving its responses. Its reputation score will not increase while the one of other mechs will.  

## V. Generalized Mech Marketplace

### V. 1. Presentation 

As written in the abstract, we envisioned two routes for deploying these services: 

1. Implement them as protocol-owned services, following the procedure described in [AIP-2](https://github.com/valory-xyz/autonolas-aip/pull/2); 
2. Enable operators to run these services as sovereign agents. 

Since the deployment of a PoSe requires a lot of effort (in particular a consortium running the PoSe), we choose the second option. Furthermore, this has the advantage of putting into competition different service providers proposing the same type of service, through the same reputation score system for mechs, and taking fees from the services in a uniform (and thus simpler) way through the marketplace. 

Furthermore, this minimizes the overlaps that one can identify in the three components as presented above, over the following functionalities: 

1. **Managing funds**: on-chain vaults which hold and manage funds (handling payments); this corresponds to RelayerContract (for Olas Relayer) and AutomationVault (for Olas Automate); in principle there could be a one set of vaults which manage funds for all the mechs in the Mech Marketplace.  
2. **Transaction relay**: both Olas Automate and Olas Relayer incorporate transaction relay (handled respectively by Executor and RelayerContract).
3. **Receiving requests**: this function is handled by EventMonitor and RelayerMonitor (Olas Automate), and Relayer (Olas Relayer); similar “entrypoint” contracts for Mech Marketplace.
 
We present below general design considerations for a “Generalized Mech Marketplace”, where mechs can act as service providers for either AI work, automation or transaction relay (as illustrated on the following picture) and later other types of tasks. We also present how fees would be taken from all these services through the marketplace.

### V. 2. Key performance indicators (KPIs) 

The two main indicators which matter for the design of the architecture are the following: 

1. **Turnover** - total value of the transactions conducted through the marketplace; 
2. **Revenue** - total value of collected fees; 

Note that the interest of the turnover is to account for the usefulness of the system. Ultimately we would count in it only useful activity and not all activity (for instance we would count as useful activity the requests which are delivered and do not lead to a negative evaluation of the service provided, here through the reputation score). 

Clearly the design of the fee model should be made so that ***ultimately*** the ***revenue generated is maximal***. Note that the fees affect the turnover: if the fees are too high, end users (Operators here) may be discouraged to use the agent services which rely on the marketplace (and go to a marketplace with lower fees), and in return a lower turnover means lower generated revenue; on the other hand a turnover increase implies higher revenue. 

Maximization of revenue implies maximization of the turnover, thus we must keep in mind these two indicators when designing the fee model. In practice we will need to define when turnover increase is high enough so that fees may be increased (this is discussed in [[VI. Description of the current payment models for mechs](#vi-description-of-the-current-payment-models-for-mechs)]).

### V. 3. General design considerations

To be clear, we call ***payment*** the transaction from the requester to the service provider and we call ***fee*** the share of this payment which is taken by the protocol as a revenue. There are two possible types of fees: ones for using the marketplace; fees collected on payments. The first one is typically a time-based subscription and won’t be discussed further, except for timeline and precise numbers. 

Regarding the design for the second type of fees, the following elements should be discussed together: 

1. **Payment model** (how the payments are made)
2. **Fee model** (what share of the payments is taken by the protocol and how)
3. **Funds architecture** (how funds are held)

Indeed, the fee model depends on the payment model, as fees are taken upon payments. On the other hand the payment model should also be determined by the fee model which makes the revenues maximal. Furthermore, their precise implementation is determined by the funds architecture, which in turn should be designed according to the properties of the payment model and the fee model.  

The marketplace is currently two-sided: users are separated into two types, which are buyers (requesters) and sellers (services). Moreover, note that the marketplace is meant to be entirely autonomous, meaning that buyers and sellers are all agents. The complete autonomy of the whole structure requires complex pricing and fee structures which should mimic as much as possible “physical” marketplaces (for instance quote and queue systems discussed below).

## VI. Description of the current payment models for mechs

Currently, there are two types of payment models for mechs: a fixed price model [[VI. 1. Base model](#vi-1-base-model)] and task-based subscription model [[VI. 3. Subscriptions](#vi-3-subscriptions)], that we discuss here. 

### VI. 1. Base model 

The base payment model for mechs is demonstrated on the following figure: 

![Mech base payment model ](../imgs/mech_payment.png?raw=true "Mech base payment model")

In this model, whenever a requester sends a request, they send funds and request to a contract (Mech contract) which transmits the request to the corresponding off-chain mech service. The service unlocks these funds after it responds to the request. The price is fixed for a mech. 

### VI. 2. With the Marketplace

With the Marketplace, the requester can send funds and requests to the MechMarketplace contract, which routes the request to a mech (the priority mech). The off-chain mech service which responds to the request (priority mech if before timeOut, or any mech that delivers first after timeOut) receives the funds. 

### VI. 3. Subscriptions 

#### VI. 3. a. Principle 

**[Nevermined](https://nevermined.app/en)** is a platform which offers (amongst other services) contracts technology in order to allow payment processes such as subscription-based ones. 

Currently, such contracts have been implemented in the Olas ecosystem on diverse chains [Gnosis ones are working, Polygon Matic, Arbitrum One, Base, Optimism, Celo Mainnet, created] in order to allow ***dynamic pricing*** (useful since AI work exhibits high variability in terms of complexity, see discussion in [[VIII. 1. Demand-dependant fees](#viii-1-demand-dependant-fees)]), meaning that price of a work sent to a mech is determined after it is delivered (and not when sending the request as above) (however currently prices depend only on the mech and not the task). This works in principle as follows: an upfront payment is made (typically of 1 xDAI) and then each time a request is delivered, payment is made using this credit (typically approximately 0.01 xDAI, which means that a subscription corresponds approximately to 100 requests allowed). When the credit is insufficient requests are denied. 

#### VI. 3. b. Implementation 

In practice, in the current implementation, a mech with a subscription is an [AgentMechSubscription](https://github.com/valory-xyz/ai-registry-mech/blob/main/contracts/integrations/nevermined/AgentMechSubscription.sol) which extends the contract AgentMech, adding a constant **subscriptionNFT**, a function **setSubscription**, and overrides the functions **_preDeliver** and **_preRequest**. This works as follows: 

1. **subscriptionNFT** is a contract managing a subscription, with which the requester interacts by subscribing and adding deposit.
2. **setSubscription** allows changing **subscriptionNFT**. 
3. **_preRequest** checks that enough funds are available before accepting an incoming request.
4. **_preDeliver** subtracts an amount equal to the price of the mech in the requester’s account in **subscriptionNFT**.   

## VII. A simple fee model 

There are two possibilities to collect fees (which can be combined): 

1. Collect a share of the initial subscription deposit (buyer side); 
2. Collect a share of the payment for each transaction (seller side).

Ultimately each share corresponds to a parameter which will be adjusted according to the observed dynamics, and may also depend on the life cycle of the marketplace. So far this means that the fee taken on a **deposit** is 

**<span style="color:#5A8ED8;">fee_deposit = share_deposit * deposit</span>**, 

where share_deposit is a float value between 0 and 1. The formula is similar for the seller side: 

**<span style="color:#5A8ED8;">fee_payment = share_payment * payment</span>**
		         
### VII. 1. Collecting fees from initial deposits

The ***first one*** would be the simplest and most cost-efficient, provided that the fee model shall be extended to other chains than gnosis (if a transaction is executed each time an action is taken by the service and fee is taken by the protocol), and that it avoids tracking all the transactions executed. On the other hand, it might be more prohibitive for users (this implies that such fees should be high later in the life cycle of the marketplace).

Let us illustrate how it would work. For instance if **10%** of the initial subscription deposit is taken by the protocol, the user would pay $111 upfront and from this deposit $11 would be directly sent to a protocol-owned address and $100 would be held in the subscription contract. 

![Fee from deposit](../imgs/fee_deposit.png?raw=true "Fee from deposit")

### VII. 2. Collecting fees from each transaction 

For the ***second one***, each time a transaction is executed, a share of the amount would be taken by the protocol. 

![Fee from transactions](../imgs/fee_trans.png?raw=true "Fee from transactions")

In practice in this diagram, no micro-payments would be executed: they would be accumulated at the level of the subscription contract, allowing the protocol to withdraw any time.

### VII. 3. In practice 

#### VII. 3. a. Without subscription 

Although, as discussed below, we will mostly want to take fees dependent on payments, we might also want to change the fee model dynamically. In practice we would want to implement this simple model first, following the two approaches at the same time, adding a fee switch allowing to combine or alternate the two approaches described in [[VII. 1. Collecting fees from initial deposits](#vii-1-collecting-fees-from-initial-deposits)] and [[VII. 2. Collecting fees from each transaction](#vii-2-collecting-fees-from-each-transaction)]. 

We suggest here changes on the **[MechMarketPlace](https://github.com/valory-xyz/ai-registry-mech/blob/main/contracts/MechMarketplace.sol)** contract as implemented currently, as follows. 

1. The contract would declare four public constants **deposit_share**, **deposit_switch**, **delivery_share**, **delivery_switch**, and two private variables **protocol_balance**, **protocol_adress**. 
2. A function **withdraw()** which can be called only by **protocol_address** (done by access control), sends the amount of protocol_balance to this address using call(), and sets the protocol_balance to zero: 

**<span style="color:#5A8ED8;">protocol_balance = 0</span>**

3. At each delivery, the protocol share is computed before paying the mech and added to **protocol_balance**: 

**<span style="color:#5A8ED8;">protocol_balance = protocol_balance +msg.value x delivery_switch x delivery_share**. 

Also subtracted payment is sent to the mech. This means that in the line 
IMech(priorityMech).requestFromMarketplace{value: msg.value}(msg.sender, data, requestId);
msg.value is replaced with msg.value * (1- delivery_switch * delivery_share).  

4. At each deposit (call of the payable function **request()**), the share of the protocol is computed and added to protocol_balance: 

**<span style="color:#5A8ED8;">protocol_balance = protocol_balance +msg.value x deposit_switch x deposit_share</span>** 


**Note**: at the moment, payment is made to the priority mech when routing the request to the priority mech (through the requestFromMarketplace function), and that if the priority mech does not deliver, the mech which delivers does not get payment. Payment would need to be made at the end of the function deliverMarketplace, to msg.sender. For this we need to add a function to the AgentMech contract to receive payment without request (and remove payment from the requestFromMarketplace function). 

#### VII. 3. b. With subscriptions 

With the Nevermined subscriptions, requests are sent through the marketplace and routed to mechs, however no funds are sent through the marketplace (in fact the function **_preRequest** reverts if an amount is sent with the request). Instead funds are taken directly by the mech from the subscription contract. 

In practice, the **AgentMechSubscription** needs to be updated, in particular replace the following line: 

if (creditsToBurn > 0) {
            IERC1155(subscriptionNFT).burn(account, subscriptionTokenId, creditsToBurn);
        }
in order to replace creditsToBurn with **creditsToBurn * (1- delivery_switch * delivery_share)**, in a similar way as above [[VII. 3. a. Without Subscription](#vii-3-a-without-subscription)]. 

For this we need to add get functions for each fee parameter (switch and value) → this will be useful later as well. Furthermore, we need a way for the mech to either: actually take some payment from the subscription contract and send it to the **MechMarketPlace** contract; or move it directly. In both cases, the funds will be directly added to **protocol_balance** when received. 

Note that ultimately, all the subscriptions could be handled directly within the marketplace contract, avoiding this last step. 

### VII. 4. Computing turnover and total fees

#### VII. 4. a. Current turnover data

For the sake of the example, assume that **10%** of the payment is taken as a fee (this is rather standard, for instance the same share is taken as a fee by Gelato for automation). In this case, it is irrelevant for this computation if fees are taken by transaction or from initial deposit.

As shown by the Dune dashboard [here](https://dune.com/queries/4167773/7014726), currently the average daily total payment to mechs is approximately **$35**. As it is the case in automation (Gelato fee), we fix the protocol share at **r = 10%** of the payment, meaning that the daily protocol fee is approximately: 

**<span style="color:#5A8ED8;">r * average_daily_request_number * average_request_price**.

With a request price of **$1** and **average_daily_request_number = 35**, we have a daily protocol fee equal to **$3.5**.

#### VII. 4. b. Activity requirements

##### VII. 4. b. i. Principle 

We expect the activity of the new services to be substantial, as these services provide important benefits to users, such as the ones described in the previous sections. This allows these services to charge a markup over their own costs (in particular gas). In turn, this enables the protocol to collect a part of the markup as a fee, which generates revenues for the protocol. 

##### VII. 4. b. ii. Formula for transaction requirements

The fee that the user/agent has to pay is then provided by the following formula (expressed in the currency chosen by the user for gas payment):

**<span style="color:#5A8ED8;">user/agent-facing fee = gas + markup</span>**,

where **<span style="color:#5A8ED8;">markup = service_markup + protocol_markup</span>**.

![Markup equation](../imgs/markup_service.png?raw=true "Markup equation")

##### VII. 4. b. iii. Example computations 

Let us provide some rough estimate of **revenue** for the protocol that automation and gas abstraction can provide. We will assume that the markup is **10%** (this is the fee that Gelato applies for smart contract automation) of the abstracted gas (expressed in $ here): 

**<span style="color:#5A8ED8;">protocol_markup = (abstracted gas)/10</span>**.

An approximate of the total markup per day, denoted total_markup is then: 

**<span style="color:#5A8ED8;">total_protocol_markup=average_protocol_markup*number_of_transactions_per_day</span>**

We have average_protocol_markup approximately equal to 
				
**<span style="color:#5A8ED8;">average_gas_usd_per_tx /10<span>**, 

so that approximately 

**<span style="color:#5A8ED8;">number_of_transactions_per_day = 10 * total_protocol_markup / average_gas_usd_per_tx</span>**

Suppose that we first target **500$** per day for **total_markup**. Let us consider the average gas price for different chains and the corresponding number of transactions per day (numbers are taken from this Dune dashboard): 

1. Ethereum: **average_gas_usd_per_tx  = 5.149**, so that **number_of_transactions_per_day = 971**;
2. Solana: **average_gas_usd_per_tx = 0.084**, so that **number_of_transactions_per_day = 59524**;
3. Polygon: **average_gas_usd_per_tx = 0.005**, so that **number_of_transactions_per_day = 1000000**.

Note that for Solana and Arbitrum for instance the gas price is virtually zero. 

In principle the distribution of the funds over multiple chains will depend on other factors than simply the gas price. Let us do an analysis which assumes an average gas of 10 Gwei. We then have **average_protocol_markup = 0.0088$**
This yields the following: **number_of_transactions_per_day = 56818**.
For comparison, the number of transactions on Ethereum is approximately 1 million per day. One can find transaction data for each chain on which the protocol is deployed: [Ethereum](https://etherscan.io/token/0x0001A500A6B18995B03f44bb040A5fFc28E45CB0), [Gnosis](https://gnosisscan.io/token/0xcE11e14225575945b8E6Dc0D4F2dD4C570f79d9f), [Polygon PoS](https://polygonscan.com/token/0xFEF5d947472e72Efbb2E388c730B7428406F2F95), [Solana](https://solscan.io/token/Ez3nzG9ofodYCvEmw73XhQ87LWNYVRM2s7diB5tBZPyM), [Arbitrum](https://arbiscan.io/token/0x064f8b858c2a603e1b106a2039f5446d32dc81c1), [Optimism](https://optimistic.etherscan.io/token/0xFC2E6e6BCbd49ccf3A5f029c79984372DcBFE527), [Base](https://basescan.org/token/0x54330d28ca3357F294334BDC454a032e7f353416), [Celo](https://celoscan.io/token/0xacffae8e57ec6e394eb1b41939a8cf7892dbdc51). 

##### VII. 4. b. iv. Mech instances requirements

Note that the increase of the number of transactions necessary to generate revenues from gas abstraction and smart contract automation is expected to follow from staking: together with on-chain contracts for gas abstraction and smart contract automation, other contracts will be deployed in order to define KPIs (introduced in AIP-4) for agent economies. These KPIs “force”, by their definition, the use of Automation, Relayer and Mech Marketplace. Anyone can participate in staking and plug in their agents to reach these KPIs, creating the needed activity within these services.

Furthermore, requirements to generate revenues for the protocol from protocol-owned services include, beyond a certain threshold of activity, a threshold in the number of available agents necessary to handle this activity. We provide an approximate of the number x of agent instances (or equivalently operators) needed, for both automation and gas abstraction. For this we assume that none of the agents are faulty. Assume for simplicity that automation tasks involve only one transaction. Estimating the average time for running such an automation task and transaction relay for the transaction to be 1 minute. Since there are 60*24 = 1440 minutes in a day, we need 
**59524/1440 ~ 41**
instances running, if on Solana for instance, and 
**971/1440 ~ 1**
on Ethereum.

|  | Transaction/day requirement (for $500 per day) | Agent instances requirement (for $500 per day) |
|----------|----------|----------|
| Solana    | 59524    | 41     |
| Ethereum | 971     | 1  |

### VII. 5. Numbers for protocol shares

In this section, we study other marketplaces and the fee that they apply (which can be a base for the fees taken in the Mech Marketplace). 

#### VII. 5. a. Freelance work marketplaces: Fiverr and Upwork 

Particular numbers can be determined by analogy with freelance work marketplaces, like Fiverr and Upwork. Here are the two models: 

Fiverr applies 5.5% fees to buyers, and 20% of earnings on the seller side; 
Upwork has a tier fee model for buyers: 5% and 10% for advanced features (see [here](https://www.upwork.com/pricing/client#pricing-table)), 10% on the seller side; 

#### VII. 5. b. Other AI agents marketplaces

On the top of these freelance work marketplaces, there are several direct competitors for the Mech Marketplace. Let us study them here. Some of them are simply platforms proposing diverse AI agent services for a subscription. Most of them do not share revenues with developers (for instance **[Beam](https://beam.ai/solutions/customer-service)**, **[agent.ai](https://beam.ai/solutions/customer-service)**, **[ILLA](https://www.illa.ai/)**), or are not commercialized (for instance **[SuperAGI](https://superagi.com/pricing/)**).
OpenAI’s **[GPT Store](https://openai.com/index/introducing-the-gpt-store/)** plans to remunerate developers (in 2025). Only **[Penlight AI](https://www.penlight.ai/)** rewards developers, who fix their pricing model (see [here](https://platform.penlight.ai/pricing)). Users pay based on usage or subscription (see [here](https://www.penlight.ai/)). The marketplace takes 15% of each transaction on the seller side (developers). 

Here are some decentralized projects similar to the Mech Marketplace (they reward developers): 

1. **[Synergetics](https://synergetics.ai/platform/agentseller/)** (AgentSeller and AgentMarket): dynamic marketplace where creators (equivalent to builders) can list, sell, or rent AI agents, models, and digital assets. They can set prices, subscription fees, and royalties while offering a platform for buyers to integrate pre-trained AI solutions into their business workflows. Note that the builders can set perpetual licenses. Use cases: financial services, healthcare and retail. 
2. **[Theoriq](https://www.theoriq.ai/)** - decentralized protocol that facilitates the creation, deployment, and monetization of AI agents within a community-governed marketplace. Developers can build and register their AI agents on Theoriq’s platform. Employs a tokenized incentive structure that rewards developers based on the value their agents contribute to the ecosystem (see [here](https://www.theoriq.ai/technology)). Pricing model: chosen by developers and subscription or usage-based. 
3. **[SwarmZero](https://swarmzero.ai/)** - decentralized platform that enables developers to create, deploy, and monetize AI agents; builders set up pricing; 
4. **[Fetch.ai](https://fetch.ai/)** - decentralized platform that enables developers to create and deploy autonomous economic agents (AEAs) for various applications. Developers can monetize their agents by offering services within the Fetch.ai ecosystem, receiving payments in FET tokens, the platform’s native cryptocurrency. Users pay for services provided by these agents using FET tokens, which are also used to cover transaction fees on the Fetch.ai network.

Note that most of them are human to agents marketplaces (with exception of fetch.ai), so the main added value of the Mech Marketplace is complete autonomy, since only agents are in principle interacting with the Marketplace. All these marketplaces apply only a subscription to the buyer side and do not take a percentage on deposits.

## VIII. Adding other fees

### VIII. 1. Demand-dependant fees

For (relatively) simple services such as gas abstraction and automation, the simple model presented above is in principle enough. On the other hand, it is in general possible to apply other fees for services which are highly variable in the following terms (with the simple purpose of increasing revenues for the protocol): 

1. How valuable the service is, according to users (here agents); 
2. The revenue potential for the protocol;

The second factor is correlated with the first, as a higher value for end-users means that they would be willing to pay higher fees. This is the case for mechs in principle. 

### VIII. 1. a. Description 

One idea, similar to *tax systems*, is to apply higher fees to high-performance services. Note that this is in favor of fees taken upon transactions rather than initial deposits. 

We assume that performance is correlated with demand (the more requests a mech gets as priority one, the higher the fees that are taken). How high the demand is depends on the activity of the mech in time (especially for services which are required in particular times) as well as the activity of other mechs. 

Ideally, the computation of these fees would be determined by machine-learning algorithms proposing demand categories (such as “high demand”, “medium demand”, “low demand” for instance). 

For the sake of proposing a model for now, let us denote by **activity_mech** the number of requests that a mech has received in the last **T= 7 days**, and max_activity the maximum of this number over all mechs currently running. Then we would apply the following fee (on the top of the base fee above of 10%) on each payment taken by a mech depending on the value of  

**<span style="color:#5A8ED8;">r_mech = activity_mech / max_activity</span>** 

| **Low demand** (<span style="color:#5A8ED8;">r_mech < 0.3</span>) | **Medium demand** (<span style="color:#5A8ED8;">0.3 < r_mech < 0.6</span> | **High demand** (<span style="color:#5A8ED8;">r_mech > 0.6</span>) |
|----------|----------|----------|
| <span style="color:#5A8ED8;">0%</span>   | <span style="color:#5A8ED8;">2.5%</span>     | <span style="color:#5A8ED8;">7.5%</span>     |

Such numbers could be adjusted a posteriori (or repeatedly) based on observed dynamics. 

Note that the mech activity could account only for delivered requests, or delivered requests which do not lead to a lower subjective reputation score. 

#### VIII. 1. b. In practice

In the current implementation of the **MechMarketPlace**, the function **deliverMarketPlace()** is used to send delivery to the marketplace, and takes the parameter requestId from which it identifies the corresponding request. 

We would add the following elements to the marketplace contract: 

1. A mapping **request_timestamps** which associates a to list of request ids and their timestamps to every mech address;
2. A function **update_request_list()** called internally each time a request is routed to a mech, and removes the requests such that **<span style="color:#5A8ED8;">block.timestamp - request_timestamp > period  (where period is a constant)</span>**;
3. A mapping **activity_based_fees** which associates a share of payment taken by the protocol on this request. It is updated whenever a request is delivered, and the fee information is used when the request is routed to a mech in order to determine the amount sent to the mech and the share taken by the protocol. 

#### VIII. 2. c. Remarks

Just like demand-related fees on the seller side, we can apply additional fees on the deposit depending on the request volume/frequency of the requester.

We may also want to apply fees depending on the overall load of the marketplace contract. If we have multiple marketplace contracts on the same chain, this would even out the workload across these marketplaces.

We would also like to incentivize certain behaviors through the fees. These include: 

1. **Innovation**: covering new use cases; regularly updating the code to ameliorate it;
2. **Competition**: how many agents are handling a certain use case (note that we need there to have an understanding of which use cases are similar); 
3. **Collaboration between agents**: for instance lower incentives for agent to agent transactions (having a signature which is specific to agents, attributed when they register and private); 
4. **Encourage higher activity** (no fees and later lower fees for high deposits).
5. **Higher code quality**: now we have a subjective score and karma, and it is possible to have a code quality score, which can be attributed by "code quality" agents or human reviewers.

### VIII. 2. Remarks

#### VIII. 2. a. Time-based subscriptions

##### VIII. 2. a. i. Description and advantages

Instead of taking a payment each time an action is performed (with incurred gas fees), one possibility for services with regular activity (such as gas abstraction and automation, AI work in certain cases) is to run on time-based subscriptions. This means that after an upfront payment, for a fixed period of time, the requester can send any number of requests. In the same way as the other subscriptions, subscription management (expiration, payment schedules) can be handled off-chain, saving gas. 

The payment of this subscription could be determined for the next period of time based on the number of tasks executed in the last period (for automation for instance, this would be the number of transactions executed). 

Furthermore, the duration of the subscription period could be left to the requester. In that case, time-dependent fees could be taken, which incentivize funds availability (larger period of time), so that funds can be used to rebalance liquidity pools, used for gas abstraction, when needed, as in principle the requesters would tend to choose shorter periods (because of changes in user need, trust issues, etc). 

Note that time-based subscriptions could be an advantage for certain requesters, as it implies cost predictability (at least for the subscription period), or because it involves monitoring activity (the service can’t be paid by the events if they are rare). 
 
For both subscriptions models, recurring payments can be set up using smart contract automation. Such a recurring payment can be terminated at any time by the requester, or automatically when the mech becomes faulty (one transaction not delivered on time).

##### VIII. 2. a. ii. Modelisation

Following the description of the above section, denote by **T** the duration of the subscription period. Also denote by **f_N** the payment for the Nth period of time a requester has made a subscription for a certain service, and **task_count_{N-1}** the number of tasks executed during the last period. This number is recorded by the service and transmitted to the subscription contract at the end of the period. 
Then the payment would be proportional to the task count: 

**<span style="color:#5A8ED8;">f_N = c * task_count_{N-1}</span>**, 

where **c** is a constant.

We choose, for the sake of the example, to apply a time-related fee equal to **r’/T** (this could be replaced with any decreasing function of **T**). Then the fee taken would be equal to 

**<span style="color:#5A8ED8;">r * f_N + r’ /T = r * c * task_count_{N-1} + r’/T</span>**
			
#### VIII. 2. b. Automated deposits

Together with subscriptions, the protocol could offer as an option the automation of adding credit on the vault whenever the credit is low enough (in order to avoid certain transactions to be reverted or suspended when credit is insufficient) - or more simply automatically send a low balance alert. The automation could be attributed to the automation service. In principle this could lead to other fees taken by the protocol (for “systematizing” the automation of deposits). 

This could be extended to other services related to the subscription itself, such as time-based subscription management described above. 

#### VIII. 2. c. Other fees

We should envision the following features, which may incur additional protocol fees: 

1. **Listing fees**: charged for “advertising” a service provider; it is relevant when the number of service providers gets high and requesters have to search through a long list; in this case there would be two options there: a service which searches for the service most adapted to the task, or a search engine; in any case, services may pay in order to appear higher in the search engine or to be considered first among equivalent services (identified by the special service). Here we should weight the reputation score and the visibility charge, with higher weight on the reputation score. 
2. **Subscription fees**: the requesters would pay one common subscription for any service included in a list of services accepting this offer. Note that the interest of this is to show the value provided by the services within the marketplace. Along with this, a free trial can be implemented for a short period of time (again for listed services).  

## IX. Adaptation of the payment model and funds architecture

We have discussed the overlaps between the three components. One overlap is the payment system: all three would rely on vault-like systems. One possibility, in order to simplify the whole architecture, is to use only one vault (one per chain) in order to handle all the payments. Furthermore, the additional fees presented in [[VIII. Adding other fees](#viii-adding-other-fees)] would also require modifications. We discuss here the possibility of refactoring the components by integrating gas abstraction and smart contract automation in the marketplace, leading to a ***“Generalized Mech Marketplace”***. We first discuss other possible models for marketplaces than the current two-sided model [[IX. 1. n-sided and hybrid marketplaces](#ix-1-n-sided-and-hybrid-marketplaces)]). We then discuss the various factors which may affect the design of the system, such as: unique vs multiple vault [[IX. 2. Common Vault contract](#ix-2-common-vault-contract)]; cross-chain optimization of operations [[IX. 3. Cross-chain considerations](#ix-3-cross-chain-considerations)]; the possibility by the service providers to specify a payment structure [[IX. 4. Service-specific payment models](#ix-4-service-specific-payment-models)]; cross-application of each component’s features (such as the reputation score of mechs for instance) in [[IX. 5. Commonized features](#ix-5-commonized-features)]; quote system for deadline and price [[IX. 6. Quote system](#ix-6-quote-system)]; security [[IX. 7. Security considerations](#ix-7-security-considerations)]; enabling any agent to become a mech [[IX. 8. Registries](#ix-8-registries)].

Based on this discussion, we present a first specification in [[X. Generalized Marketplace and its workflow](#x-generalized-marketplace-and-its-workflow)].

### IX. 1. n-sided and hybrid marketplaces

In this section we discuss marketplaces in general. This will give us an idea of how the implemented marketplace could evolve, and a general idea of what fees are taken by marketplaces in order to apply similar ideas. 

Marketplaces can be one-sided (for instance Craigslist), two-sided (for instance Airbnb), or multiple-sided (Google: the three sides correspond to the websites, advertisers and searches), according to the type of persons/moral persons involved in the marketplace.

At the moment, the Mech Marketplace is a two-sided marketplace which acts as an intermediate between requesters (AI agents in principle) and mechs. As discussed below, it would be ultimately possible for this marketplace to become one-sided (requesters can also be mechs) or even a three-sided “hybrid” marketplace, in which the protocol itself uses some services. 

### IX. 2. Common vault contract

Here the advantages and disadvantages of each of two extreme options (unique vault vs multiple vaults), regarding in particular the fee model. Here are the important points for this choice: 

1. **Security**: the least number of vaults, the simpler the architecture. This means in particular lower probability of technical issues or security issues. This makes sense if the payment structure and fee structure are relatively uniform (same fees for all services for instance). The more dependency introduced in terms of payments and fees on the type of service and on the particular service, the more complexity is added to this unique contract. With higher complexity within the contract, the higher is the risk: separate vaults localize the risks - if one vault encounters a problem (e.g., a bug or security breach), it won’t affect the other services. On the other hand, managing multiple vaults adds operational complexity. 
2. **Flexibility**: having one unique vault can enhance flexibility in terms of fee collection and management, as each service can have its own payment system amongst proposed ones with associated fee system, which may as well be dependent upon current activity (performance-based fees). 
3. **Cross-service transactions**: if requesters are interacting with multiple services (such as for instance automation of deposit discussed in [[VIII. 2. b. Automated deposits](#viii-2-b-automated-deposits)]) and the system needs to move funds between them, a unique vault simplifies cross-service payments. This is the case in particular if for instance gas abstraction and/or automation is systematically integrated with each delivery. 

Provided that we would want to apply demand-related fees, the unique vault option seems ultimately to be the most appropriate.

On the other hand, this is a *single point of failure*. Furthermore, there is a risk of overload. For this reason, we may want to deploy multiple identical contracts on each chain and share the same data which are synchronized regularly (using automation for instance). 

### IX. 3. Cross-chain considerations

#### IX. 3. a. Discussion 

By deploying this system on multiple chains, mech operations can be “chain-optimized”. The point is that certain chains are most adapted to certain types of work, for they focus on higher security (when the work concerns sensitive data), or lower gas fees (important for heavy data processing and frequent transactions for instance). Furthermore, it may be optimal for mechs to register on the chain(s) where the corresponding demand is located.

On the gnosis chain, gas is virtually zero, but we might want to use other chains (which might benefit particular use cases) for which gas is not neglectable. In this case whenever an agent has to send requests repeatedly, this can incur high gas costs. It is possible in principle to reduce these gas costs via subscriptions, by only taking from the credit once a certain number of requests have been sent (say 10 for instance) or when the credit is exhausted. This can be handled by a counter off-chain.

In practice, we should expect that services which execute specific types of work are registered on the optimal chain (reaching a trade-off between chain-optimisation and location of the demand). In practice it may well happen that the requester and the mech are located on different chains. In order for requesters holding certain tokens to access services and for services to collaborate, we would implement bridges between the different chains. Note that in principle certain services can also execute certain works for which the corresponding chain is not optimal. There could be a service, running on all chains, which, provided a certain work, advises which chain is optimal for it.

On the other hand, we should envision that mechs/services may collaborate. This implies in particular  that requests can be sent and delivered across chains. 

#### IX. 3. b. Implementation 

##### IX. 3. b. i. Cross-chain workflow

The architecture would be as follows. One marketplace contract is deployed on each chain, with one bridge for each pair of chains. 

Requests are sent with the address of the mech and data about the chain it is registered on.

![Cross chain i](../imgs/cross_chain_1.png?raw=true "Cross chain i")

Funds are bridged if necessary and the request is routed to the corresponding marketplace and then to the priority mech. 

##### IX. 3. b. ii. Taking over

If the priority mech does not deliver in time, there are two possibilities: 

1. Restrict deliver calls to mechs which are registered on the same chain as the priority mech; 
2. Allow any other mech to deliver. 

In the second case, it would be inefficient to bridge funds before the request is delivered, as it may happen that they have to be bridged twice. 

In that case, funds are kept on the marketplace contract where funds are deposited, and request is routed to the other marketplace. When a mech delivers, it specifies as parameter the chain on which the request was made, the funds are bridged from the request marketplace to the delivery marketplace and then sent to the mech: 

![Cross chain ii](../imgs/cross_chain_2.png?raw=true "Cross chain ii")

##### IX. 3. b. iii. Saving bridging fees

Note that in order to minimize costs due to bridging funds (this implies gas), one possibility is not to bridge funds each time a request is sent, but simply send “virtual” funds which are recorded by each contract as debts. Then whenever the debt of a contract is large enough (if for instance it represents at least a certain percentage of the total funds in the contract), a double transaction is sent to settle debts, using the bridges.

It is possible to take a fee from the requester for this debt system, and is taken after delivery from the funds before bridging.

##### IX. 3. b. iv. Remarks

Note that in the gas of gas abstraction, we should expect the service to be available on all chains, so that it is optimal for the requesters to send gas abstraction requests on the chain corresponding to the token they pay with (including gas tokens, OLAS, and stable coins). 

With subscriptions, this would work in a similar way. When creating a subscription, after funds are routed to the marketplace contract on the other chain, a subscription is created on this other chain.

#### IX. 3. c. Example use cases

In predictive markets, three mechs could collaborate in order to make predictions. One mech (Mech-2) would analyze on-chain financial data and another one (Mech-3)  would aggregate external data sources (for instance financial news) and apply sentiment analysis. The third mech (Mech-1) would combine information in order to make the prediction. 

The flow would go as follows. Mech-1 receives a request from an agent, and sends one request to Mech-2 and one to Mech-3, and waits for the deliveries. When both have answered, Mech-1 does additional work and then delivers the request by the agent. All the requests and deliveries are made through the marketplace. 

In terms of payments, there are two options. 1. They can be made in series: first Mech-1 takes payment after delivering and then Mech-2 and Mech-3 are paid by Mech-1; 2. Otherwise, counts are kept at the level of the marketplaces and then one transaction is made for each of the mechs 1,2,3. 

![Cross chain iii](../imgs/cross_chain_3.png?raw=true "Cross chain iii")

The reputation score update for this kind of case should be: that if Mech-2 or Mech-3 is faulty, only them have a decrease in reputation score and not Mech-1. This should be handled at the level of the marketplaces, which would record “request chains” instead of only requests, and whenever the request is not delivered by the priority mech, the mechs at the “origin” of the fault have reputation score decreased by 1 (the others score don’t change). 

Note that for this to make sense, we need that **timeOut_1** for Mech-1 is at least the one **timeOut_2** of Mech-2 and **timeOut_3** of Mech-3:

**<span style="color:#5A8ED8;">timeOut_1 > max(timeOut_2,timeOut_3)</span>**

One application of this schema is when Mech-2 and Mech-3 train different models, and Mech-1 combines these models in order to provide probabilities for a binary event for instance. 

The requests to Mech-2 and Mech-3 can be also sequential, for instance if they handle different parts of the data workflow: for instance aggregating real-time data (news); training a model on these data. The first type of work would benefit running on a chain with higher KB/s, while the second on a chain with higher Mgas/s. 

### IX. 4. Service-specific payment models 

As discussed above, there are multiple possibilities for payment models. Some may be more adapted to certain services than others. We would thus want to leave the choice of the payment model to the service, and the payment model could also be a particular service. 

Beyond the simple payment to the service, such payment models could incorporate the possibility for the requester to pay an additional surcharge for having their request processed with higher priority. For instance this surcharge payment could follow a certain pattern [for instance “high priority”, “medium priority” and “low priority”], from which the protocol would take a higher share when priority is high. The service could prioritize the highest bidders (however this would create bidding wars that we would like to avoid). 

In terms of take-over (whenever a service is faulty, another one of the same type should take over), any other service of the same type can deliver. For this it is better for the payment model to be proposed amongst options in the marketplace contract. At any time the requester can maintain a list of subscriptions to different types of services with specific funds in them, each of which is accessible to a certain type of services to take over. 

### IX. 5. Commonized features

We describe here how the different features of the three components, as they are specified, can be put in common in the generalized marketplace. 

#### IX. 5. a. Request handling 

##### IX. 5. a. i. Description 

A unique contract similar to the MechMarketPlace contract could receive all the requests and route them to a service, and route the deliveries back to the requester when answered (when relevant). The same contract also receives the funds and transfers them to the service. 

There are two possibilities there: 

1. The request is sent with the information of a subscription in the marketplace contract which contains the information of the service it is associated with.  
2. The request is sent to a priority service specified in the request. 

Requests are processed in a uniform way and their meta-data contains a type of service (for instance [“transaction_relay”, “automation”,”ai_work”]) which tells the marketplace to which other services the request can be sent when the target service becomes faulty. Requests can be creation or modification of the subscriptions in the marketplace. This abstraction can be done in such a way that other types of services may be easily integrated later. 

Note that currently both mechs and agents are staked on a staking contract. Only staked services should be able to deliver requests or be subscribed.

Furthermore, in principle, the marketplace could be “one-sided”: deliverers could be requesters as well (in order for different services to collaborate).

##### IX. 5. a. ii. Special cases

In the case of Olas Automate, the requester is not “there” all the time, meaning that the request sent by the requester consists of an algorithm structured as follows: monitoring events and gathering data, executing some code when an event happens and then executing a transaction. In this case the payment model is systematically a subscription. 

#### IX. 5. b. Reputation score

##### IX. 5. b. i. Extending to other services

In principle, the reputation score for mechs can be extended to other types of services (Karma + subjective score determined by the requesters). In a similar way as in the mech marketplace, the priority service has to respond within a fixed period of time to deliver, and after this period any other service can deliver. However it must be of the same type. When running on a subscription, the requester can designate the subscribed service as faulty and any other service can take over the subscription if no other particular service is designated by the requester (via a modification request). 

Note though that in the case of automation, the reputation score is more difficult to define, as part of the automation work is to monitor events. The time period within which it has to deliver is thus determined by the automator. Only a subjective component can be taken into account (possibility to change the automator handling the jobs and attribute a score). 

##### IX. 5. b. ii. Queue system 

Currently, the requester specifies the deadline for the priority mech to deliver. Although a minimum is fixed by the marketplace, this is in principle a vulnerability of the system, as agents can send requests to a mech with a deadline which is too short for the required work. The authorized deadlines should be fixed by each mech/service, and its karma is decreased when not matching the agreed deadlines. Furthermore, deadlines should ultimately be fixed by a quote system. 

Also, if a mech is overloaded with requests, the karma of the mech should not decrease after a certain period of time from receiving the request. We should be careful that the request is sent to a mech only when it is "ready" to take care of it. Request queues should be handled at the level of the marketplace, and a mech can decide manually to take the request, and time starts counting from this time. The information of the workload of each mech should be public so that agents can decide to wait or not (and go for a less reputable and less loaded mech).

![Queue system](../imgs/queue_system.png?raw=true "Queue system")

Furthermore, the Karma system leaves the possibility of the priority mech to deliver after the deadline has passed. In that case, the Karma of the priority mech should still decrease (potentially not by 1, but 0.5 for instance): 

1. Delivery by priority mech and **t_delivery > timeOut**:   Karma += -0.5
2. Delivery by priority mech and **t_delivery < timeOut**: Karma += 1
3. Delivery by other mech and **t_delivery > timeOut**: Karma += -1 

An overall reputation score can be defined to rank services, which can be a combination of subjective_score and karma: 
		
**<span style="color:#5A8ED8;">reputation_score = a * avg(subjective_score) + b * karma</span>**, 

where **a** and **b** are parameters which can be adjusted according to the dynamics of the marketplace. 

##### IX. 5. b. iii. On the cold-start problem 

Note that when the marketplace implements a reputation score, the ***cold-start problem*** may occur. This means that services entering the marketplace have to compete with services having a high reputation score. The entering services may not receive any requests and leave it. In order to increase the retention rate, marketplace can implement the following: 

1. Services enter with an average reputation score. Note that in the case of Karma, what would this reputation score be, and how would it be computed ?
2. Incentives for testing entering services (these can be subtracted from fees). We would track how many requesters have entered a score and a threshold on this number for a service to be considered as new. 
3. Visibility can be increased for some determined period of time when entering. 

### IX. 6. Quote system 

Eventually the price of a service should vary depending on the work, and this price should be agreed upon by the requester and the mech/service before the request is received. The same applies to the deadline. 

The way this would work is that a request would specify a type of work (on the top of the type of service). For instance: “ai_work” or “quote” for a mech. The mech delivers a “quote” request by sending back two parameters: **deadline** and **price**. There are two possibilities here: this proposal is rejected and the request is canceled; or the proposal is accepted and the request is sent to the mech. 

![Quote system](../imgs/quote_2.png?raw=true "Quote system")

Note that for this to make sense, the price of the quote should be significantly lower than the typical price of a work and should be fixed. 

Furthermore, the two types of requests (work and quote) should correspond to two different queues, so that these two queues are handled in such a way that the processing speed is higher in the quote request queue. 

### IX. 7. Security considerations

#### IX. 7. a. With the current implementation 

Vault contracts must have a function **withdraw()** which enables the service to take payment once an action has been taken. When the contract is specific to a service, we need to ensure that only a specific service can call this function (the owner). In a system with a unique vault contract, this function **withdraw()** can be called by multiple services. It would be needed that only a unique service can call the function with certain inputs. 

In the first case, the modifier **onlyOwner** is used. 

In the second case, the contract holds a mapping which basically associates a balance to a tuple (requester_address,service_address): 

**mapping(address => mapping(address => uint256)) public balances**;

Anyone can call the function, but if the caller address is not among the registered service addresses, the call is reverted.  

Note that currently, the payment is made directly on the mech contract through the wrap **request()** of the function **_request()** via the keyword payable (see the code [here](https://github.com/valory-xyz/ai-registry-mech/blob/main/contracts/AgentMech.sol)). 

Furthermore, one can add multi-signatures requirements. 

When using a subscription, security is ensured at the level of the subscription contract.

#### IX. 7. b. In the generalized marketplace

In the generalized marketplace, subscriptions would be handled at the level of the marketplace contract, and we can add a level of security by enabling only the corresponding to call the credit burn function on a subscription inside the marketplace.

Note that handling these subscriptions at the level of the marketplace would make it easier for the protocol to take fees from payment made using a subscription contract. Furthermore, the marketplace would also handle the payment to the mech when the credit becomes insufficient or enough time has passed. 

### IX. 8. Registries

#### IX. 8. a. Any agent can become a mech

In this model, any agent which is registered in the Service Registry can become a mech. Let us provide details on how to do this. 

At the moment, there are two registries: the [AgentRegistry](https://github.com/valory-xyz/ai-registry-mech/blob/main/contracts/AgentRegistry.sol) (for agents acting as mechs) and [ServiceRegistry](https://github.com/valory-xyz/autonolas-registries/blob/main/contracts/ServiceRegistry.sol) (for all agents). The aim of the first one is to register mechs, which can deliver incoming requests from the mech marketplace. In order to simplify the architecture, we envision the possibility of merging these two registries into one. This implies that any agent registered in the ServiceRegistry can register on the MechMarketplace, thus becoming a mech. 

The registration process on the MechMarketplace must create a Mech contract for the agent, so that it can interact with the MechMarketplace (this is the only requirement).  

As ServiceRegistry is already deployed, we only introduce changes in the mech marketplace side. The aim will be to connect the MechMarketplace contract to ServiceRegistry. 

The simplest way to do this would be to integrate the functionality of the AgentFactory’s function **create** in the **MechMarketplace** contract and modify it slightly, by removing the line 

**agentId = IAgentRegistry(agentRegistry).create(agentOwner, agentHash)**;

and adding agentId as an input of the function (the id of the agent as registered in the ServiceRegistry). All the checks in the deliverMarketplace function (for a mech to deliver through the marketplace) are left unchanged. 

From this point, we assume that before registering as a mech, an agent has functionalities required for a mech (if not, the mech shall be ultimately excluded by its reputation score).

We would need to check as well that the agent is registered, using an interface with the ServiceRegistry contract.

#### IX. 8. b. Staking requirements

At the moment, the MechMarketPlace contract performs diverse checks on both the requester and mech sides, in order to ensure that only staked requesters (resp. mechs) can send (resp. deliver) a request through the marketplace.

One reason for requiring that the requesters are staked is to avoid manipulations of the reputation score. On the other hand, we would like to make the marketplace available rapidly to a wide range of requesters for co-marketing reasons. 

The simplest way to achieve this would be to add a flag **allow_all_requester** to the MechMarketPlace contract so that when True, the function request of MechMarketPlace does not perform **checkRequester**, and when False it does. 

This way, different marketplaces can be deployed for the two purposes described above. 

Preemptively, we would as well add a feature for the mech side. 

## X. Generalized marketplace and its workflow 

### X. 1. Generalized Marketplace specification 

[[III. 4. Mech Marketplace](#iii-4-mech-marketplace)] contains a specification of the Mech Marketplace. We would introduce the following modifications in order to create a generalized marketplace. When relevant, we point at the discussion subsection above.

#### X. 1. a. Funds holding 

Funds would be held in a way which is specific to an agent and a mech. This means that we add the following elements to the Mech Marketplace contract: 

1. [[IX. 4. Service-specific payment models](#ix-4-service-specific-payment-models)] A mapping **subscription_type** which associates to every pair (requester,mech) a value among the following ones (eventually we may consider other types):  [“none”,”time_based”,”activity_based”].
2. [[IX. 4. Service-specific payment models](#ix-4-service-specific-payment-models)] A mapping **subscription_contract** which to every pair (requester,mech) associates a subscription contract if the subscription type is “time_based” or “activity_based”. 
3. [[IX. 4. Service-specific payment models](#ix-4-service-specific-payment-models)] A function **deposit()** which allows a requester to add a deposit. It takes as parameter the mech address, and adds a deposit on the subscription contract following its logic.
4. [[IX. 4. Service-specific payment models](#ix-4-service-specific-payment-models)] A function **update_subscription_contract()** which changes the subscription contract.  

Another possibility would be to have a subscription contract per requester which handles all the transactions with mechs.  

Another one would be to handle subscriptions inside the marketplace contract. In this case, we would need additionally: 

1. [[IX. 4. Service-specific payment models](#ix-4-service-specific-payment-models)] A mapping which to a pair (requester,mech) associates a value which corresponds to time credit in case the subscription type is “time_based”, and funds when the subscription type is “activity_based”. 
2. [[IX. 4. Service-specific payment models](#ix-4-service-specific-payment-models)] The **deposit()** function directly adds funds to this mapping in the second case, and in the first case convert funds into time and then add it to the mapping. 
3. [[IX. 4. Service-specific payment models](#ix-4-service-specific-payment-models)] A function **update_subscription_type()** which changes the subscription type, taking as parameters the requester address, the service address and the subscription type (note that this allows termination of a subscription).  

#### X. 1. b. Requests and delivers handling

1. The **request()** function would be adapted to add as a parameter the type of request (gas abstraction, AI work, automation).
2. Add to each service a constant work_type, taking values in [“ai_work”,”gas_abstraction”,”automation”]. 
3. The **deliver()** function would include checking that the type of the service corresponds to the type of the request, otherwise the call is reverted. This is to ensure that only services of the same type can deliver a request. Depending on the type of request, data is made available. In any case an event is emitted.  

#### X. 1. c. Payments management

1. [[VII. Adding other fees](#vii-adding-other-fees)] Add a function **compute_fees()** which determines, when **deliver()** is called, what is the total share taken by the protocol. It can only be called by the marketplace contract when a service delivers (in the function **deliver()**), and determines which share of the payment is accumulated by the protocol and which share is accumulated by the service (this data is stored in a funds mapping). It depends on the subscription type, and possibly options taken by the requester to automate deposit [[VIII. 2. b. Automated deposits](#viii-2-b-automated-deposits)]. 
2. When **deliver()** is called, the payment is taken according to the subscription type: from the subscription if there is, or from the contract funds otherwise. 


#### X. 1. d. Fee management

1. [[VII. Adding other fees](#vii-adding-other-fees)] Add a mapping from fee names to fee values, and a function **modify_fees()** which can be called only by the protocol-owned address, which takes as a parameter the fee name and new value. Note that each fee value corresponds to the share taken by the protocol. 
2. [[VII. Adding other fees](#vii-adding-other-fees)] Add a mapping from fee names to booleans, which specifies which fees are applied and a function **switch()** which takes as a parameter a fee name and changes the corresponding value to opposite value in the mapping. 
3. [[VII. Adding other fees](#vii-adding-other-fees)] A function **withdraw()** which allows the protocol-owned address or any service to retrieve total fees accumulated at the level of the marketplace contract. It takes an address as a parameter, sets to zero the funds in a corresponding mapping and sends the funds to the address.

#### X. 1. e. Reputation scoring

We would add the following elements to the Karma contract: 

1. A mapping **subjective_score** from (mech,agent_id) to integers which represents a subjective score. 
2. A function **update_subjective_score()** which can be called by any agent with their id in the registry as parameter and a subjective score value and sets the **subjective_score** entry to this value.

The remainder of the Karma contract is left unchanged, and the mapping **mapMechKarma** is updated only when a single request is delivered.

#### X. 1. f. Cross-chain handling 

1. Each marketplace contract has a mapping which to each other marketplace contract associates a number which corresponds to debt. 
2. Whenever a request is received, the marketplace contract checks on which chain the priority service is and if on another chain, calls a function **relay_request()** which checks if debt after adding the entering funds is “too high”. If so, it settles debt with the other marketplace contract. In any case it then adds a quantity corresponding to entering funds to debt.

#### X. 2. Workflow and diagram 

In this section, we propose a specification for the generalized marketplace following the discussions of [[IX. Adaptation of the payment model and funds architecture](#ix-adaptation-of-the-payment-model-and-funds-architecture)], with a workflow description. 

We assume that all payments are managed by the marketplace contract, which also receives requests. 

The workflow would be as follows:

1. Requester sends a request and/or funds, including type of request and a priority service. 
2. If the service is on another chain, the request is routed to the marketplace contract on the other chain and debt is recorded.  
3. The contract transmits the request to the priority service. 
4. Service takes action, and for each action takes payment from the funds available for the requester on the marketplace contract, in a specific subscription whenever there is one, or from the marketplace contract otherwise. 
5. If the requester does not have a subscription with the service, if the priority service does not deliver in time, any other service of the same type on the same chain can take over and take payment. The karma is updated accordingly. Fee is taken at the moment of the payment and sent to a protocol-owned address. 

!["Overview Specification"](../imgs/final.png?raw=true "Overview Specification")

## XI. Front-end 

Given the complexity of the set of fees that would be applied, that they may as well change in time based on decisions coming from the protocol side, we will ideally have some front-end, which makes it possible to change the value of each of the parameters corresponding to fees (including turning the switch on and off) in order to study their effect or to adjust according to the dynamics. These adjustments will depend on the life cycle of the marketplace. 

As the choice of these parameters depends on the dynamics of the whole system, such an application should monitor its activity. In particular, it should track: 

1. The total number of requests sent;
2. The total number of requests delivered; 
3. Average delivery time; 
4. Average transaction value; 
5. Total liquidity held; 
6. Balance between the funds in the different chains; 
7. The total of fees; 
8. Requesters behaviors (for instance how many times a certain requester changes a subscribed service), and services behaviors; 
9. Relation between the subjective score and Karma and discrepancies between the two; 
10. Total dissatisfaction (karma going down + subjective reputation score below current average);
11. The relation between the use of services and the reputation score system;
12. How much the services are centralized (applies to services which are “similar”); 
13. Number of users joining/leaving the marketplace;

## XII. Fee table 

Here is a table summarizing the different fees discussed. Each of them may in principle depend on the service (AI work, gas abstraction, automation), which means that they would correspond to as many parameters as types of services in the marketplace. 

| Fee name | Description | Hyperlink |
|:-----------|:------------:|------------:|
| deposit_fee       | Fee taken when the requester deposits funds         | [section VII. 1.](#vii-1-collecting-fees-from-initial-deposits)        |
| delivery_fee       | Fee taken when the request is routed to a mech (or service in general).        | [section VII. 2.](#vii-2-collecting-fees-from-each-transaction)        |
| low_demand_fee,medium_demand_fee, high_demand_fee       | Fee which is applied to payment taken by a mech (or service in general) when it has low/medium/high demand          | [section VIII. 1.](#viii-1-demand-dependant-fees)        |
| priority_access_fee       | Applied on payment surcharge when the requester wants their request to be treated with higher priority.        | [section IX. 4.)](#ix-4-service-specific-payment-models)        |
| cross_chain_fee       | Fee applied for cross-chain optimized mech operations         | [section IX. 3. b. iii.](#ix-3-b-iii-saving-bridging-fees)    |

## XIII. Execution timeline considerations

The possibility will be left to the protocol to decide if certain fees are taken or not via a “fee switch” that can be turned on or off in order to adjust this trade-off. 

### XIII. 1. Implementation

Ultimately the payment model should depend partially on the fee model. In the immediate future, the simpler option (taking fees upon the initial deposit) could be implemented first, and then more complex fees could be implemented later, combining the two options, eventually adjusting according to the observed dynamics. 

All the fees should be implemented in a modular way in order to manipulate them separately. 

In the immediate future, changes suggested in [[section VII. 3.](#vii-3-in-practice)] can be implemented first, with experimental parameters. Provided that deposit fees can be prohibitive in early stages, we would fix deposit_share and deposit_switch to zero. Only deliver_switch will be 1. In order to fix the value of the constants deliver_share, there are three reasonable options: 

1. Rely on early users/community feedback
2. A/B testing - study responses to fee changes (elasticity of demand and supply). 
3. Using an analogy with freelance service providers platforms (like Fiverr or Upwork), we can apply similar or lower fees at the beginning. We can keep in mind to have a target in this range (or possibly higher), but start lower (for instance 2.5%). Note though that it is difficult to increase fees without adding value. 

### XIII. 2. Fee schedule

The fees should change according to the life cycle of the marketplace. In the early stage, traction is needed more than revenues, thus fees should be low. They would be progressively set higher as turnover gets higher. We need to set milestones in terms of turnover in order to schedule fee increases (and which fees are increased/turned on). 

For instance, a growth rate of 20-30% month-over-month would be a sign of strong demand, and we would consider increasing the fees at this stage. Another indicator would be the retention rate (for instance 60% retention rate after 3 months), or a threshold in the number of transactions per day or month, or how frequent active users make transactions. 

We could also imagine a system which updates automatically the fee parameters by “gradient descent” (the loss functions depending on the total fees generated over a certain period of time, taking into account turnover). 


## XIV. Rationale

## XV. Security Considerations

The proposal implementation will have to undergo multiple audits.

## XVI. Test cases

## XVII. Implementation 

## XVIII. Copyright

Copyright and related rights waived via \[CC0\](([https://creativecommons.org/publicdomain/zero/1.0/](https://creativecommons.org/publicdomain/zero/1.0/)).

---

## Table of contents

[I. Simple Summary](#i-simple-summary)

[II. Abstract](#ii-abstract) 

[III. Motivation](#iii-motivation)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[III. 2 Olas Automate](#iii-2-olas-automate)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[III. 3. Olas Relayer](#iii-3-olas-relayer)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[III. 4. Mech Marketplace](#iii-4-mach-marketplace)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[III. 5. Protocol-owned services as fee-capture mechanisms](#iii-5-protocol-owned-services-as-fee-capture-mechanisms) 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[III. 5. a. Idea](#iii-5-a-idea) 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[III. 5. b. Computing activity requirements](#iii-5-b-computing-activity-requirements) 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[III. 5. b. i. Formula](#iii-5-b-i-formula)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[III. 5. b. ii. Example computations](#iii-5-b-ii-example-computations)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[III. 5. c. Agent instances requirements](#iii-5-c-agent-instances-requirements) 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[III. 6. Revenues splitting](#iii-6-revenues-splitting)

[IV. Specification](#iv-specification)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IV. 1. Olas Automate](#iv-1-olas-automate)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IV. 2. Olas Automate](#iv-2-olas-relayer)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IV. 2. a. Subsection breakdown](#iv-2-a-subsection-breakdown)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IV. 2. b. RelayerContract](#iv-2-b-relayer-contract)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IV. 2. c. Relayer](#iv-2-c-relayer)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IV. 2. d. Workflow Diagram](#iv-2-d-workflow-diagram)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IV. 3. Mech Marketplace](#iv-3-mech-marketplace)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IV. 3. a. MechMarketPlace](#iv-3-a-mechmarketplace)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IV. 3. b. Karma](#iv-3-b-karma)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IV. 3. c. Workflow](#iv-3-c-workflow)

[V. Generalized Mech Marketplace](#v-generalized-mech-marketplace)

&nbsp;&nbsp;&nbsp;[V. 1. Presentation](#v-1-presentation)

&nbsp;&nbsp;&nbsp;[V. 2. Key performance indicators (KPIs)](#v-2-key-performance-indicators-kpis)

&nbsp;&nbsp;&nbsp;[V. 3. General design considerations](#v-3-general-design-considerations)

[VI. Description of the current payment models for mechs](#vi-description-of-the-current-payment-models-for-mechs)

&nbsp;&nbsp;&nbsp;[VI. 1. Base model](#vi-1-base-model)

&nbsp;&nbsp;&nbsp;[VI. 2. With the Marketplace](#vi-2-with-the-marketplace)

&nbsp;&nbsp;&nbsp;[VI. 3. Subscriptions](#vi-3-subscriptions)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VI. 3. a. Principle](#vi-3-a-principle)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VI. 3. b. Implementation](#vi-3-b-implementation)

[VII. A simple fee model](#vii-a-simple-fee-model)

&nbsp;&nbsp;&nbsp;[VII. 1. Collecting fees from initial deposits](#vii-1-collecting-fees-from-initial-deposits)

&nbsp;&nbsp;&nbsp;[VII. 2. Collecting fees from each transaction](#vii-2-collecting-fees-from-each-transaction)

&nbsp;&nbsp;&nbsp;[VII. 3. In practice](#vii-3-in-practice)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VII. 3. a. Without subscription](#vii-3-a-without-subscription)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VII. 3. b. With subscriptions](#vii-3-b-with-subscriptions)

&nbsp;&nbsp;&nbsp;[VII. 4. Computing turnover and total fees](#vii-4-computing-turnover-and-total-fees)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VII. 4. a. Current turnover data](#vii-4-a-current-turnover-data)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VII. 4. b. Activity requirements](#vii-4-b-activity-requirements)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VII. 4. b. i.  Principle](#vii-4-b-i-principle)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VII. 4. b. ii. Formula for transaction requirements](#vii-4-b-ii-formula-for-transaction-requirements)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VII. 4. b. iii. Example computations](#vii-4-b-iii-example-computations)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VII. 4. b. iv. Agent instances requirements](#vii-4-b-iv-agent-instances-requirements)

&nbsp;&nbsp;&nbsp;[VII. 5. Numbers for protocol shares](#vii-5-numbers-for-protocol-shares)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VII. 5. a. Freelance work marketplaces: Fiverr and Upwork](#vii-5-a-freelance-work-marketplaces)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VII. 5. b. Other AI agents marketplaces](#vii-5-b-other-ai-agents-marketplaces)

[VIII. Adding other fees](#viii-adding-other-fees)

&nbsp;&nbsp;&nbsp;[VIII. 1. Demand-dependant fees](#viii-1-demand-dependant-fees)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VIII. 1. a. Description](#viii-1-a-description)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VIII. 1. b. In practice](#viii-1-b-in-practice)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VIII. 2. c. Remarks](#viii-2-c-remarks)

&nbsp;&nbsp;&nbsp;[VIII. 2. Remarks](#viii-2-remarks)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VIII. 2. a. Time-based subscriptions](#viii-2-a-time-based-subscriptions)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VIII. 2. a. i. Description and advantages](#viii-2-a-i-description-and-advantages)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VIII. 2. a. ii. Modelisation](#viii-2-a-ii-modelisation)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VIII. 2. b. Automated deposits](#viii-2-b-automated-deposits)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[VIII. 2. c. Other fees](#viii-2-c-other-fees)

[IX. Adaptation of the payment model and funds architecture](#ix-adaptation-of-the-payment-model-and-funds-architecture)

&nbsp;&nbsp;&nbsp;[IX. 1. n-sided and hybrid marketplaces](#ix-1-n-sided-and-hybrids-marketplaces)

&nbsp;&nbsp;&nbsp;[IX. 2. Common vault contract](#ix-2-common-vault-contract)

&nbsp;&nbsp;&nbsp;[IX. 3. Cross-chain considerations](#ix-3-cross-chain-considerations)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 3. a. Discussion](#ix-3-a-discussion)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 3. b. Implementation](#ix-3-b-implementation)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 3. b. i. Cross-chain workflow](#ix-3-b-i-cross-chain-workflow)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 3. b. ii. Taking over](#ix-3-b-ii-taking-over)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 3. b. iii. Saving bridging fees](#ix-3-b-iii-saving-briding-fees)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 3. b. iv. Remarks](#ix-3-b-iv-remarks)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 3. c. Examples of use cases](#ix-3-c-examples-of-use-cases)

&nbsp;&nbsp;&nbsp;[IX. 4. Service-specific payment models](#ix-4-service-specific-payment-models)

&nbsp;&nbsp;&nbsp;[IX. 5. Commonized features](#ix-5-commonized-features)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 5. a. Request handling](#ix-5-a-request-handling)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 5. a. i. Description](#ix-5-a-i-description)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 5. a. ii. Special cases](#ix-5-a-ii-special-cases)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 5. b. Reputation score](#ix-5-b-reputation-score)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 5. b. i. Extending to other services](#ix-5-b-i-extending-to-other-services)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 5. b. ii. Queue system](#ix-5-b-ii-queue-system)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 5. b. iii. On the cold-start problem](#ix-5-b-iii-on-the-cold-start-problem)

&nbsp;&nbsp;&nbsp;[IX. 6. Quote system](#ix-6-quote-system)

&nbsp;&nbsp;&nbsp;[IX. 7. Security considerations](#ix-7-security-considerations)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 7. a. With the current implementation](#ix-7-a-with-the-current-implementation)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 7. b. In the generalized marketplace](#ix-7-b-in-the-generalized-marketplace)

&nbsp;&nbsp;&nbsp;[IX. 8. Registries](#ix-8-registries)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 8. a. Enabling agents to become-mechs](#ix-8-a-enabling-agents-to-become-mechs)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[IX. 8. b. Staking requirements](#ix-8-b-staking-requirements)

[X. Generalized marketplace and its workflow](#x-generalized-marketplace-and-its-workflow)

&nbsp;&nbsp;&nbsp;[X. 1. Generalized Marketplace specification](#x-1-generalized-marketplace-specification)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[X. 1. a. Funds holding](#x-1-a-funds-holding)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[X. 1. b. Requests and delivers handling](#x-1-b-requests-and-delivers-handling)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[X. 1. c. Payments management](#x-1-c-payments-management)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[X. 1. d. Fee management](#x-1-d-fee-management)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[X. 1. e. Reputation scoring](#x-1-e-reputation-scoring)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[X. 1. f. Handling cross-chain](#x-1-f-handling-cross-chain)

&nbsp;&nbsp;&nbsp;[X. 2. Workflow and diagram](#x-2-workflow-and-diagram)

[XI. Front-end](#xi-front-end)

[XII. Fee table](#xii-fee-table)

[XIII. Execution timeline considerations](#xiii-execution-timeline-consideration)

&nbsp;&nbsp;&nbsp;[XIII. 1. Implementation](#xiii-1-implementation)

&nbsp;&nbsp;&nbsp;[XIII. 2. Fee schedule]($xiii-2-fee-schedule)

[XIV. Rationale](#xiv-rationale)

[XV. Security Considerations](#xv-security-consideration)

[XVI. Test cases](#xvi-test-cases)

[XVII. Implementation](#xvii-implementation)

[XVIII. Copyright](#xviii-copyright)




---
title: Smart Contract View
date: 2020-10-15T00:00:00+01:00
menu:
  cordapp-design-language:
    parent: cdl-views
    identifier: cdl-smart-contract-view
    weight: 12

tags:
- this
- that
- the other
---

# CDL - Smart Contract View

In this section we will describe the CDL Smart Contract view. We will explain What a Smart contract is in Corda, how the Smart Contract view adds structure to our CorDapp designs and use an example use case to build up to the following full Smart Contract view:

{{< figure zoom="./resources/cdl-agreement-smart-contract-full.png" width="800" title="Click to zoom image in new tab/window" >}}



## What is a Corda Smart Contract?

First things first, 'Smart Contract' is a term that is often used in the distributed ledger/ blockchain space but not often used in the context of discussing Corda. Corda has the building blocks of other blockchains' Smart Contracts but they are chopped up and split into different parts:

- The data of a Smart Contract is stored in the ContractStates on ledger.
- The functions or things you can do to that data are defined in the Contract.
- The way which those functions are enacted is via building new transactions in the Flows which uses the states and conform to the Corda Contracts.

So its all there, but not in a neat encapsulated package. Which is a good thing because this approach actually leads to a great deal of flexibility. For examples, the Flows are not just limited to enacting a transition on the Ledger, they are a powerful messaging framework which allows you to send anything you like to counterparties with confidence.

For the purposes of CDL we are going to define a Corda Smart Contract as the combination of one or more types of ContractState which are constrained by a Corda Contract. We'll leave the Flows out of it as we are agnostic as to how the transactions get built up, we will only concern ourselves with the validity of the final transaction.

So our Smart Contracts are basically a combination of data (ContractStates) and what we're allowed to do with it (Contracts).

When we draw a Smart contract view diagram we will always specify the ContractState(s) and the contract which will implement the behaviour we are articulating.


## CDL adds structure to Smart Contracts

For many simple CorDapps, including the ones in our Corda example repos, you could make the argument that you don't need a special way of representing CorDapp designs. If you have a simple state, with a single implied status and only one or two commands, you can probably just write the contract code, which will fit on one screen, and see exactly what's going on. However,if you want to do anything beyond a basic PoC things are likely to get more complicated, especially if you are writing production grade CorDapps that need to deal with many different scenarios.

As Complexity increase it quickly becomes difficult to 'think in code' and the corresponding risk of making mistakes goes up.  The primary benefit of CDL is that it provides a level of abstraction that allows you to think about more complicated designs without having to hold all the details in your head.

The way the abstraction works is to split the elements of the Smart Contract design into a standard set of considerations:
- What's my state data,
- what different statuses can the data be in,
- Who are the participants for each status,
- How can I move between each status,
- etc...

By splitting the design process into these different predefined considerations, we are adding structure and robustness to our design thinking, by providing a standardised way to visually represent each of these considerations in a neat single page view, we are further empowering the design to communicate and reason about the design, and by defining standardised ways to implement and test each consideration we increase confidence in the faithful implementation of the design.


## Corda Smart Contracts define what you can't do, not what you can

The normal programming paradigm is that in a given context you have a set of available functions that take inputs and produce outputs. Other Blockchains, for example Ethereum, use this approach: what you can do with a Solidity Smart Contract is defined by the methods which are made available to the user of the Smart Contract.

Corda Smart Contracts work in a different way. Corda lets the creator of the transaction do what ever they want as long as it conforms to the rules set out in the Contract class. This approach gives massive flexibilty for the CorDapp developer, but that flexibility shouldn't necessarily be passed on to the user of the Smart Contract as it can increase the risk of misuse.

In the extreme case, if the contract class verify() function is empty the transaction can contain anything. This might be fine depending on what your CorDapp is doing, but, for example, it's not fine if the security of the CorDapp assumes that your counterparty can't change the price that they are agreeing to pay for the goods you are shipping them when, because of a lack of adequate constraints, they can.

So if the actions that a Smart Contract user can take needs to be limited, which is most cases, then the Smart Contract designer needs to have confidence that the constraints in the Smart Contract adequately box in the user.

The structure provided by CDL aims to help the Smart Contract Designer make sure there are no 'Holes in the Fence' which unwanted behaviour can slip through.

Note, in most CorDapps the actions a user can take are limited by the Flows which are provided in the CorDapp. But it is important to remember that Flows can be, and to an extent are intended to be, rewritten by each Counterparty to suit their particular implementation requirements. The Corda guarantee of shared data and logic is only valid at the Smart Contract/ transaction level. Controls written into the Flows are by their nature not as secure as controls written into the Smart Contracts.


## Modelling using Status

So, we know we need structure, what should that structure be based on?

An underlying assumption of CDL is that the States in the Smart Contract can be in different 'statuses' and that in different statuses we want to define the different form that a transaction can or must take.

This is similar to a Finite State Machine in computer science. The classic example of which is a clothes washing machine that has two states, 'Full of water' and 'Empty'. The washing machine's Finite State Machine should have the constraint that the user can only execute the 'Open door' command when the washing machine is in the 'Empty' State. If it doesn't, somebody is going to end up with a very wet floor.

So statuses become a key driver of Smart Contract behaviour.

Note, for simple Smart Contracts there may only be one, implicit status. The Smart Contract view works there's just only one status box with all actions and constraints hanging of that one box.

## Building up the Smart Contract view

Over the following sections we will introduce the elements of the Smart Contract View.

We will use a simple negotiation use case, where a buyer is negotiating to buy some goods from a seller for an agreed price. The requirements can be summarised as follows:

- There should be a buyer and a seller who are represented as Parties on a Corda Network.
- The negotiation should result in an agreement recorded on the Corda Ledger which has the conscious and informed consent of both parties.
- The agreement should record the buyer, seller, description of the goods and the price agreed.
- Either party should be able to propose an agreement, with the other party either consenting to or rejecting the proposal.
- The proposer should be able to change their mind and revoke the proposal prior to the other party consenting.
- When a agreement is agreed, then a billing tracker must increment which tracks Network usage for the purpose of billing.
- Privacy must be maintained such that Parties to an agreement can't see previous agreements performed on the network to which they were not a party
- The Business Network Operator who manages the Agreement Service should not see details of the agreements made on their network.

We will build up to the full Smart Contract view diagram which will look like this:

{{< figure zoom="./resources/cdl-agreement-smart-contract-full.png" width="800" title="Click to zoom image in new tab/window" >}}

### States

The first element of the diagram to consider is the Primary state type, in this case there is only one state type in the Smart Contract and we will call it the AgreementState. States are represented as rounded boxes with the following three sections:

**Header**: Contains the class name (type) of the state, which must implement the ContractState interface, and the status that the state is in.

**Properties**: Contains the properties in the state class together with their classes.

**Participants**: Contains the participants, ie the Parties that will get a copy of any transaction in which this state is included as an input or output (but not reference state)

{{< figure zoom="./resources/cdl-agreement-smart-contract-state.png" width="350" title="Click to zoom image in new tab/window" >}}

For our example use case we want the following Properties:

- buyer - the buyer of the goods
- seller - the seller of the goods
- goods - a description of the goods being sold
- price - the price of the goods being sold
- proposer - the Party who is proposing the agreement
- consenter - the party who is consenting to the proposed agreement
- rejectionReason - a text field for supplying an explanation of why a proposed agreement was rejected
- rejectedBy - the party who rejected the proposed agreement

Note that rejectionReason and rejectedBy are nullable as they will not be required in all statuses.

As we want this to be a private transaction, we want the participants to be limited to the buyer and seller. ie both the buyer and seller will receive copies of the transactions but nobody else. (Although we must later consider who gets copies of transaction through the transaction resolution process)


## Statuses

Once we have defined the state, we need to show the different statuses that the state can be in. We do this by having a copy of the State for each status.

{{< figure zoom="./resources/cdl-agreement-smart-contract-statuses.png" width="800" title="Click to zoom image in new tab/window" >}}

The properties shown in each status box may differ. This allows the designer to highlight the properties which are salient for that status even though they are actually a view of the same underlying class which must implement the union of all properties on the diagram.

In this example the participants remain consistent in all statues but this will not always be the case.


Note, to prevent an explosion of complexity, CDL mandates the following rules:
- We define the Primary state type as the state type in the Smart Contract which has a status field.
- There can only be one Primary state type per Smart Contract, although there can be multiple Smart Contracts operating and interacting in a single transaction each of which have their own Primary state type.
- Other state types in this Smart Contract cannot have a status field.
- A transaction's inputs can only contain primary states with a single status.
- A transaction's outputs can only contain primary states with a single status, although this can be different from the input status.

(Don't worry too much about these rules to start with, you won't hit the level of complexity where they come in to play yet. They are designed to avoid statuses branching and taking parallel paths through the Smart Contract allowed paths, eg a transaction with an input state in the PROPOSED status should not be allowed to have two output states one with REJECTED and one with AGREED statuses.)


### Paths Constraints

Once we have the statuses defined we need to articulate how the Smart Contract can transition between those statuses. We do this by defining Path constraints.

The path for a transaction consist of
- The primary input states' status
- The primary output states' status
- The number of primary input states
- The number of primary output states
- Any additional states other than the Primary state types, including how many of them there are as inputs, references and outputs

Correspondingly a Path constraint define for a given primary input state status a permitted combination of:
- The primary output state status.
- The transaction command.
- The multiplicity of the input states for the primary state type.
- The multiplicity of the output states for the primary state type.
- Additional States, where we specify additional state types which must be present in the transaction and their multiplicities (we won't cover those here).

Path contraints are shown on the diagram as arrows between state statuses, the status at the beginning of the arrow represents an input state in a transaction, the status at the end of the arrow represents an output state in a transaction. The diagram below shows the Paths constraints for this Smart Contract and highlights how when the input status is PROPOSED there are two transitions that can be made:

 - Path 1: 'PROPOSED -- Agree --> AGREED'
 - Path 2: 'PROPOSED -- Reject --> REJECTED'

{{< figure zoom="./resources/cdl-agreement-smart-contract-paths.png" width="800" title="Click to zoom image in new tab/window" >}}

A black circle indicates no state, hence an arrow coming out of a black circle means there can be no inputs of the primary state type in the transaction. An arrow going into a black circle means there are no outputs of the primary state type in the transaction.

The command is indicated on the Path constraint arrow and the input and output multiplicities are shown at the start and end of the arrow respectively.

An important aspect of the Smart Contract view is that only paths conforming to a shown path constraint are permitted. In this example the implication is that it is forbidden to go from, say, REJECTED straight to AGREED, or from no-state straight to REJECTED.


### Multiplicities

The multiplicities indicate how many of the State type can/ must be in the transaction.

Input multiplicities are shown at the beginning of the Path constraint arrow, and can take the following values:

- 0 : There must be no input states of the primary type in the transaction.
- 1 : There must be exactly one input state of the primary type in the transaction.
- n : There can be n input states of the primary type in the transaction.

Output multiplicities are shown at the end of the Path constraint arrows, and can take the following values:

- 0 : There must be no output states of the primary type in the transaction.
- 1 : There must be exactly one output state of the primary type in the transaction.
- 1: Matched : There must be exactly one output state of the primary type in the transaction and the output state’s linearId must match the input State’s LinearId.
- m : There are m output States of this type in the Transaction.

The options above can be combined to form ranges, eg

- 0..n : there can be zero to n states of the primary state type

For example, in our diagram,  Path1 'PROPOSED -- Ageee --> AGREED':
 - The input multiplicity is 1, indicating there must be one and only one input state of type AgreementState in status 'PROPOSED'
 - The output multiplicity is 1: Matched, indicating there must be one and only one output state of type AgreementState and that the LinearID must match that of the input state.

The following example of a CashState shows how multiplicities can be expressed as ranges:

![cash-state](./resources/cdl-overview-cashstate.png)


## Signing Constraints

An important aspect of any CorDapp is the permissioning model, in other words who needs to sign which transactions. In Corda, the required signers are attached to the Transaction's Command. We mirror this in the CDL Smart contract view by including the require signer in brackets underneath the Command on the Path constraint.

{{< figure zoom="./resources/cdl-agreement-smart-contract-signing.png" width="800" title="Click to zoom image in new tab/window" >}}

The signer is usually defined in terms of some property in the State. You would not usually specify that a particular Party, say PartyA must sign, instead you would specify that the party referenced in the seller property needs to sign, which may on some instances of the state happen to be PartyA.

It is also important to specify which state you are referenceing for the property. Properties can change between the input state and the output state, you need to specify which.

In our example when performing a Repropose command, the output.proposer must sign, this may well be a different value from the input.proposer.

In some use cases signing constraints can get more complicated. A deliberately convoluted example might be: The buyer or seller must sign unless an escrow agent has presented a state of type X before the 3rd Tuesday in May. If this is the case, its best to use a separate call out to explain the constraint in more detail.


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

## What is a Corda Smart Contract

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

Corda Smart Conracts work in a different way. Corda lets the creator of the transaction do what ever they want, as long as it conforms to the rules set out in the Contract class. This approach gives massive flexibilty, but that flexibilty comes with risk.

In the extreme case, if the contract class verify() function is empty the transaction can contain anything. This might be fine depending on what your CorDapp is doing, but, for example, it's not fine if the security of the CorDapp assumes that your counterparty can't change the price that they are agreeing to pay for the goods you are shipping them when, because of a lack of adequate constraints, they can.

So if the actions that a Smart Contract user can take needs to be limited, which is most case, then the Smart Contract design needs to have confidence that the constraints in the Smart Contract adequately boxes in the user. We don't want to leave any holes in the fence.






- constrinats -> by type
- secific way to annotate each constraint in a n intuitive way
- ie add structure


- we are taking a format where you can do whatever you want, and constraining it so you can guarentee that you can only do specific sub set of things.

- represents a different way of thinking.
Corda is deliberately designed to let you do whatever you want in your Smart contracts. you can write almost anything that Java/ Kotlin allows you to do. this is very powerful, however, with great power comes the danger that it will have un intended consequences.

You want to be able to do anything when defining your smart contracts, you don't wnat the user of those smart contracts to be able to do anything.
cdl aims to give a structure which lets the Smart contract designer box in the capabilities of the smart contract user to a very tightly defined set of allowed actions.

in other Smart contract languages you would write specific functions for each thing that you wanted the user to be able to do. Eg solidity, you write a smart contract with a set of methods, eg issue payment or make transfer. You can't do anything other than those actions because the smart contract doesn't have functionality to do anything else. Corda is different, the users can put together any transaction they like as long as it doesn't double spend input states, and satidfies what every, sometimes minimal, constraints are written into the contract.

If you want to constraint the user to a small set of actions, for example those that define the allowed transitions in a business workflow, you have to work quite hard to box in the capabilites using constraints.

CDl gives a structure to help make sure there aren't any 'holes in the fence' which unwanted behaviour can slip through.


you can do anything as long as you obey this rule, --> you can only do these very narrowly defined actions.


## Modelling using Status





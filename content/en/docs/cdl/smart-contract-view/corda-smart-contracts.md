---
title: Corda Smart Contracts
date: 2020-10-15T00:00:00+01:00
menu:
  cordapp-design-language:
    parent: cdl-smart-contract-view
    identifier: cdl-smart-contract-view-corda-smart-contracts
    weight: 20

tags:
- cdl
- cordapp design language
- smart contract
- cordapp diagram
---


# Corda Smart Contracts

First things first, 'Smart Contract' is a term that is often used in the distributed ledger/ blockchain space but not often used in the context of discussing Corda. Corda has the building blocks of other blockchains' Smart Contracts but they are chopped up and split into different parts:

- The data of a Smart Contract is stored in the ContractStates on ledger.
- The functions or things you can do to that data are defined in the Contract.
- The way which those functions are enacted is via building new transactions in the Flows which uses the states and conform to the Corda Contracts.

So its all there, but not in a neat encapsulated package. Which is a good thing because this approach actually leads to a great deal of flexibility. For examples, the Flows are not just limited to enacting a transition on the Ledger, they are a powerful messaging framework which allows you to send anything you like to counterparties with confidence.

For the purposes of CDL we are going to define a Corda Smart Contract as the combination of one or more types of ContractState which are constrained by a Corda Contract. We'll leave the Flows out of it as we are agnostic as to how the transactions get built up, we will only concern ourselves with the validity of the final transaction.

So our Smart Contracts are basically a combination of data (ContractStates) and what we're allowed to do with it (Contracts).

When we draw a Smart contract view diagram we will always specify the ContractState(s) and the contract which will implement the behaviour we are articulating.

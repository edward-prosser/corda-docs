---
title: Privacy Overlay
date: 2020-10-15T00:00:00+01:00
menu:
  cordapp-design-language:
    parent: cdl-views
    identifier: cdl-privacy-overlay
    weight: 16

tags:
- cdl
- cordapp design language
- ledger evolution
- cordapp diagram
---

# The Privacy Overlay

Privacy is a key consideration when designing CorDapps. In most use cases Parties on the ledger will want to keep their corda states representing private deals, asset, permission tokens etc.. private. There's normally two aspects to this:

- Keeping states private from the network operator, this avoids one (centralised) node holding all information about the activity network.

- Keeping states private from other Parties on the network who are not entitled to see the states, ie they we're part of the deal.

For the immediate transaction being constructed ensuring privacy is relatively straight forward, the required Parties to the states are added to the participants property of each state and Corda will make sure that the union of those participants across all the states in the transaction get a copy of the transaction.

However, there is a gotcha here. For any transaction to be proved valid the Corda node needs to know the input states are also valid. It does this by getting hold of the transaction that created those input states and rerunning the verify() checks on that transaction. Of course that transaction may also of had input states which need verifying too, so the Corda node must receive and verify the transaction that created those input states as well. This continues recursively back through the ledger DAG until there are no more input states to verify. This process is called transaction resolution and the states that are resolved is sometimes called the backchain.

This is a very powerful approach because it removes the need for all Parties on the ledger to hold a full copy of the ledger, which some blockchains require, leading to scalability issues. Instead Corda acts like a wallet where only the transactions required to validate a transaction are stored on a particular node's ledger.

The gotcha is, that if there is anything in the backchain which the current node shouldn't see, then there is a privacy leak. This might include:

- Who has owned an asset in the past.
- The details of assets exchanged previously for assets in the current transaction.
- The details of deals which lead to the exchanging of assets.

The good news is that with proper CorDapp design these scenarios can be avoided and to help with that good design we have the Ledger Evolution view Privacy Overlay.

# Billing Example

To illustrate the Privacy Overlay and how it can be used effectively we are going to show two implementations of the Billing mechanism referred to in our Agreement Smart Contract.

The initial requirements for the Billing mechanism are:

- The Business Network Operator 'Agree Corp', who has built and runs the Agreement CorDapp wants to bill the users of the Network based on usage.
- Whenever a deal is finalised on the network, as indicated by a transaction with an 'Agree' command Agree Corp should receive notice of that activity.
- AgreeCorp must receive that notice before the (off ledger) invoicing date, not necessarily at the time of finalisation of the Agree transaction.
- Agree Corp should have the ability to vary the billing rate per agree transaction at any time.

The intuitive approach to meeting these requirements is to create a BillingState which is required by the Agreement Smart Contract to be included in each Agree transaction.

The Smart Contract for this BillingState might look like this:











# Start at the end

The privacy overview




# Private Billing

Given the privacy leaks identified we need to add two further Billing requirements:

- AgreeCorp must not receive a copy of any agreements made on its Network
- Parties who are not participants on a agreement must not receive copies of an Agreement







Transactions which include a fungible asset that includes

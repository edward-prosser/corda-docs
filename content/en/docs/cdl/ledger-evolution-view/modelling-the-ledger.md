---
title: Modelling the ledger
date: 2020-10-15T00:00:00+01:00
menu:
  cordapp-design-language:
    parent: cdl-ledger-evolution-view
    identifier: cdl-ledger-evolution-view-modelling-the-ledger
    weight: 10

tags:
- cdl
- cordapp design language
- ledger evolution
- cordapp diagram
---

# Modelling the Ledger

The Corda Ledger can be considered as a Directed Acyclic Graph (DAG) in which:
- States are created as outputs to a transaction
- States are consumed as inputs to transactions
- Commands are properties of transactions
- Parties sign transactions

We could illustrate this as follows:

{{< figure zoom="../resources/cdl-ledger-dag.png" width="800" title="Click to zoom image in new tab/window" >}}

However, it is difficult to get all the information we want to show into this format so we will us a similar form but modify the graph's nodes to closer align to the representations of states already introduced in the Smart Contract view.

We will also not show the signers to the transaction as separate node, instead we will just attach them to the transactions directly.

---
title: "CDL Overview"
date: 2020-10-15T00:00:00+01:00
menu:
    CorDapp-design-language:
      identifier: cdl-overview
      weight: 10

tags:
- this
- that
- the other
---

# CorDapp Design Language (CDL)

Welcome to the CorDapp Design Language Documentation. This guide will walk you through what CDL is and how to use it to effectively document, analyse, and communicate your [CorDapp](../corda-os/4.6/cordapp-overview.md) designs.


## What is CorDapp Design Language?

CorDapp Design Language (CDL) is a set of diagrammatic views you can use to concisely and accurately guide the design of your CorDapp. Using CDL in the design phase of your CorDapp allows you to:

- Represent and reason about core aspects of the Corda design including: Ledger data, shared Logic, permissioning, and privacy.
- Reason about more complex CorDapp designs through abstraction from the code and standardisation.
- Produce complete, well thought-out CorDapp designs.
- Document standardised design patterns.
- Degrade gracefully. You don't always have to put in all the detail, but it should be self-consistent at the level of detail you choose.

There are three main CDL views which you can use to communicate different aspects of your CorDapp design: Smart Contract, Ledger, and Business Process Modelling Notation (BPMN). These views are discussed further below.


## Why was CorDapp Design Language developed?

CDL was initially created to improve how CorDapp designs were communicated between our Professional Services team and their clients. They found that too much time was being spent trying to work out how to communicate an idea rather than discussing the merits of the idea itself. 'Made-up-on-the-fly' design representations could be interpreted in different ways by different people, leading to confusion.

As CorDapp designs became more complex, it was increasingly difficult to 'think in code'; there was too much going on to have confidence that all eventualities had been considered. As Corda transactions can represent high-value business activity where mistakes can cost material amounts of money, these considerations are especially important.

With the latest version of CDL, we introduce approaches for standard implementations for CDL designs, which should further improve the reliability of CorDapps designed using CDL. See the [CDL to code](cdl-to-code.md) an d[Testing CDL code](cdl-testing.md) sections.

We also have an example implementation of the Agreement Smart Contract that we will develop as we explain each of the CDL views, this can be found in the [corda/cdl-example](https://github.com/corda/cdl-example) repo. Although it is important to stress this is for guidance only and does not represent an R3 product, or code that should be relied on in production.



## Who is CorDapp Design Language for?

CorDapp Design Language is for anyone who wants to be able to articulate and understand non-trivial CorDapp designs. This may include:

- CorDapp Architects
- CorDapp Developers
- Business Analysts
- Application Testers
- Quality Assurance Analysts
- Security Analysts
- Technical Auditors

It can also be used to communicate to non-technical audiences, for example in Marketing materials, though most likely in a simplified form with degraded accuracy.


## What are the main CDL views?

Currently, there are three main CDL views, each of which serve a different purpose in communicating a CorDapp design:

### The Smart Contract view

This is the primary view of CDL - it represents the design of your Corda Smart Contracts. In Corda, Smart Contracts consist of one or more Corda States, which represent the data which will go on the Ledger, together with a Corda Contract which constrains what can be done with the data in those states.

{{< note >}}
In earlier versions of CDL, the Smart Contract view was known as the State Machine View.
{{< /note >}}

An example Smart Contract View:

{{< figure zoom="./resources/cdl-agreement-smart-contract-full.png" width="800" title="Click to zoom image in new tab/window" >}}

This diagram shows the three statuses an Agreement can have, together with the possible transitions from one status to another, and the constraints governing both statuses and transitions. The black filled circle on the left represents the initial, uncreated, state of an Agreement, which is brought into existence by being proposed, while the circle on the right represents the terminal, completed state in which the agreement has been fulfilled.

The base assumption behind the view is that Corda States can be in a number of 'statuses' which might represent, for example, different stages on a multiparty workflow. We define rules for how the Smart contract can move between statuses and the constraints which must be satisfied with each transition.

This view is conceptually modelled on a Finite State Machine. The classic example of which is a washing machine that has two states: 'Full of water' and 'Empty'. The washing machine's Finite State Machine should have the constraint that the user can only execute the 'Open door' Command when the washing machine is in the 'Empty' State. If it doesn't, someone is going to end up with a very wet floor.

For simple CorDapp Smart Contracts, there may only be one, implicit status. The Smart Contract view can still be used to communicate the design, it just devolves down to a diagram with only one state box. See the CashState Example in the previous sections.


A detailed explanation of the elements which make up the Smart Contract view can be found [here](cdl-smart-contract-view.md)

A Lucidchart template with the CDL Smart Contract view standard shapes can be found here ** add link **

In addition, the section [CDL to Code](cdl-to-code.md) section shows a standardised way to transform the CDL Smart Contract view into code.


### The Ledger view (with Privacy overlay)

If the Smart Contract defines a Smart Contract's design, the Ledger view is showing that Smart Contract in action.

The Corda Ledger can be considered as a Directed Acyclic Graph (DAG) of states and transactions connected by input/output relationships, in which states are generated as outputs to a transaction and go on to be consumed as the inputs of other transactions, with the transactions having associated properties such as the commands and who signed them.

You can't, nor is it useful to, attempt to show the whole Corda ledger. However, it is useful to show a sub-graph of the wider Ledger which concerns a particular set of transitions for the Smart Contract in question, and that's what the Ledger view does.

An example Ledger view:


{{< figure zoom="./resources/cdl-agreement-naive-billing-ledger-evolution-charlie.png" width="800" title="Click to zoom image in new tab/window" >}}

Here both states and transactions are represented as "nodes" in the graph. There is a connecting arrow from a transaction to a state when the state is an output of the transaction, and from a state to a transaction when the state is consumed by that transaction.

The Ledger view also has an additional Privacy overlay represented as the purple and orange lines in the above diagram. Each overlay is from the perspective of a Party on the ledger. It starts with a state which the Party is a participant on and then traces back through the DAG to show all the previous transactions the Party's node will receive a copy of through the transaction resolution mechanism. The green circles indicate that privacy has been preserved, where as the red circles indicate a privacy leak.

With this view, you have insight on privacy from the design phase. An unintended privacy leak can be a show stopper for a CorDapp. It's best to consider this early on, not at the point where the customers Infosec team won't sign off the deployment.


A detailed explanation of the elements which make up the Ledger Evolution view can be found [here](cdl-ledger-evolution-view.md)

A Lucidchart template with the CDL Ledger view standard shapes can be found here ** add link **


### The Business Process Modelling Notation (BPMN) views

The Business Process Modelling Notation View is, as the name suggests, a BPMN diagram showing the process flows of your CorDapp. It can be used to describe the macro Business Process flows, or alternatively the back and forth communication within a Corda Flow.

An example BPMN view:

{{< figure zoom="./resources/cdl-bpmn-simultaneous-action.png" width="750" title="Click to zoom image in new tab/window" >}}

The notation follows BPMN v2 standards, with a few small additions:

- Where a Corda transaction is written to the ledger, this is represented as a simultaneous task/action on the swim lane of every participant to the transaction. Each state is marked with a small Corda logo.
- The initiator of the transaction should be indicated on the version of the action that appears in the initiator's swim lane.
- An optional Blue box can be added to show further salient details about the transaction, such as the name of the Flow which triggered it or the command used in the transaction.

Lucidchart has a standard Shapes library for BPMN 2.0 diagrams

A detailed explanation of the elements which make up the BPM view can be found [here](cdl-bpmn-view.md)

## CDL use case example: Smart Contract view

You may wish to represent a Smart Contract made up of a CashState and CashContract using the Smart Contract view:

![cash-state](./resources/cdl-overview-cashstate.png)

This shows a CashState with its properties and participants. It then shows the various constraints imposed on that CashState by the CashContract, specifically:
- Which contract commands can be used in transactions.
- Who must sign the transaction when each command is used.
- The number of input and output states allowed for each transition.
- The specific rules which operate for the 'Pay' Command.

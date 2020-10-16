---
title: "CDL Overview"
date: 2020-10-15T00:00:00+01:00
menu:
    cordapp-design-language:
      identifier: cdl-overview
      weight: 10

tags:
- this
- that
- the other
---

# CorDapp Design Language (CDL)

Welcome to the CorDapp Design Language Documentation. This site will guide you through what CDL is, why it was developed and how to use it to effectively document, analyse and communicate your CorDapp (Corda Distributed Application) designs.


## What is Cordapp Design Language?

Corda Design Language (CDL) consists of a series of diagrammatic views which aims to concisely and accurately represent CorDapp designs. It aims to:

- Allow Engineers to represent and reason about core aspects of the corda design including: Ledger data, shared Logic, permissioning and privacy.
- Enable more complex Cordapp designs to be reasoned about than would be otherwise possible, through abstraction from the code and standardisation.
- Encourage good CorDapp designs.
- Enable the documentation of standardised design patterns.
- Degrade gracefully. You don't always have to put in all the detail, but it should be self consistent at the level of detail you choose.

For example, you may represent a Smart Contract made up of a CashState and CashContract using the Smart Contract view:

![cash-state](./resources/cdl-overview-cashstate.png)

This shows a CashState with its properties and participants. It then shows the various constraints imposed on that CashState by the CashContract, specifically:
- Which contract commands can be used in transactions.
- Who must sign the transaction when each command is used.
- The number of input and output states allowed for each transition.
- The specific rules which operate for the 'Pay' Command.

CDL is not just Marketecture (pretty pictures to sell stuff), it is primarily to communicated engineering designs. Having said that the diagrams look nice so by all means use them to sell stuff too.

## Why was CorDapp Design Language developed?


CorDapp Design language came out of a need for R3 Professional Services to be able to discuss CorDapp designs with their clients. Too much time was being spent trying to work out how to communicate an idea rather than discussing the merits of the ideas themselves. There was also the risk that 'made-up-on-the-fly' design representations could be interpreted in different ways by different people, leading to confusion.

It also became clear that as CorDapp designs became more complex, it was difficult to 'think in code', there was too much going on to have confidence that all eventualities had been considered and covered off, especially important when we consider that Corda transactions can represent high value business activity where mistakes can cost material amounts of money. A way was needed to abstract the design concepts out of the code to allow the more complex designs to be reasoned about with confidence.

With the latest version of CDL, we introduce approaches for standard implementations for CDL designs, which should further improve the reliability of CorDapps designed using CDL.



## Who is Cordapp Design Language for?

CorDapp Design Language is for anybody who wants to be able to be able to articulate and understand non-trivial CorDapp designs. This may include:

- CorDapp Architects
- CorDapp Developers
- Business Analysts
- Application Testers
- Quality Assurance Analysts
- Security Analysts
- Technical Auditors

It can also be used to communicate to non-technical audiences, for example in Marketing materials, though most likely in a simplified form with degraded accuracy.



## What are the main CDL views?

Currently, there are three main CDL views, each of which serve a different purpose in communicating a CorDApp design:

### The Smart Contract view

This is the primary view of CDL, in that it it the expression of the design of your Corda Smart Contracts. In Corda Smart Contract consist of one or more Corda States, which represent the data which will go on the Ledger, together with a Corda Contract which constrains what can be done with those the data in those states.

Note, In earlier versions of CDL, the Smart Contract view was know as the State Machine View.

An example Smart Contract View (click to zoom image in new tab/window):

{{< figure zoom="./resources/cdl-agreement-smart-contract-full.png" width="800" title="Click to zoom image in new tab/window" >}}

The base assumption behind the view is that Corda States can be in a number of 'statues' which might represent, for example, different stages on a multiparty workflow. We define rules for how the Smart contract can move between statuses and the constraints which must be satisfied with each transition.

It is conceptually modelled on a Finite State Machine. The classic example of which is a clothes washing machine that has two states, 'Full of water' and 'Empty'. The washing machine's Finite State Machine should have the constraint that the user can only execute the 'Open door' Command when the washing machine is in the 'Empty' State. If it doesn't, somebody is going to end up with a very wet floor.

For simple CorDapp Smart Contracts, there may only be one, implicit statues. The Smart Contract view can still be used to communicate the design, it just devolves down to a diagram with only one state box. See the CashState Example in the previous sections.


A detailed explanation of the elements which make up the Smart Contract view can be found here **add link **

A Lucidchart template with the CDL Smart Contract view standard shapes can be found here ** add link **

In addition, the section *** add link ** shows a standardise way to transform the CDL Smart Contract view into code.


### The Ledger view (with Privacy overlay)

If the Smart Contract defines a Smart Contract's design, the Ledger view is showing that Smart Contract in action.

The Corda Ledger can be considered as a Directed Acyclic Graph (DAG) in which states are generated as outputs to a transaction and go on to be consumed as the inputs of other transactions, with the transactions having associated properties such as the commands and who signed it.

You can't, nor is it useful to, attempt to show the whole Corda ledger. However, it is useful to show a sub-graph of the wider Ledger which concern a particular set of transitions for the Smart Contract in question, and that's what the Ledger view does.

An example Ledger view (click to zoom image in new tab/window):


{{< figure zoom="./resources/cdl-agreement-private-billing alternative-full.png" width="800" title="Click to zoom image in new tab/window" >}}



The Ledger view also has an additional Privacy overlay represented as the purple and orange lines in the above diagram. Each overlay is from the perspective of a Party on the ledger. It starts with a state which the Party is a participant on and then traces back through the DAG to show all the previous transactions the Party's node will receive a copy of through the transaction resolution mechanism.

This is especially important aspect of CorDapp design to consider. An unintended privacy leak can be a show stopper for a CorDapp. Best to consider it at the design stage, not at the point where the customers Infosec team won't sign off the deployment.


A detailed explanation of the elements which make up the Ledger view can be found here **add link **

A Lucidchart template with the CDL Ledger view standard shapes can be found here ** add link **


### The Business Process Modelling Notation (BPMN) views

The Business Process Modelling Notation View, is as the name suggests a BPMN diagram showing the process flows of the CorDapp. It can be used for describing the macro Business Process flows, or alternatively the back and forth communication within a Corda Flow.


An example BPMN view (click to zoom image in new tab/window):

{{< figure zoom="./resources/cdl-bpmn-simultaneous-action.png" width="750" title="Click to zoom image in new tab/window" >}}

The notation follows BPMN v2 standards, with a few small additions:

- Where a Corda transaction is written to the ledger  this is represented as a simultaneous task/ action on the swim lane of every participant to the transaction, each state is marked with a small corda logo.
- The initiator of the transaction should be indicated on the version of the action that appears in the initiator's swim lane
- an optional Blue box can be added to show further salient details about the transaction, such as the name of the Flow which triggered it or the command used in the transaction.

Lucidchart has a standard Shapes library for BPMN 2.0 diagrams








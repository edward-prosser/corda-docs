---
title: Corda transactions
date: 2020-10-15T00:00:00+01:00
menu:
  cordapp-design-language:
    parent: cdl-bpmn-view
    identifier: cdl-bpmn-view-corda-transactions
    weight: 30

tags:
- cdl
- cordapp design language
- business process modelling notation
- bpmn
- cordapp diagram
---

# Corda Transactions

After the Buyer has decided what they would like to buy, they need to initiate a Propose Corda Transaction. We show corda transactions as a set of duplicated actions in the swim lane of each participant in the corda transaction with the corda logo in the corner.

We join the actions with a two way dashed line. In BPMN dashed lines denote messages and are normally unidirectioal. However, in the CorDapp BPMN view we want to reflect that there a series of back and forth messages which result in a finalised transaction on the ledger so we use a two way arrow.

In addition we mark up which party initiated the transaction and annotate the message line on the Initiator end with a blue box giving more details about how thr transaction was formed, typically what flow is invoked and what the command in the transaction shuold be.


{{< figure zoom="../resources/cdl-bpmn-agreement-process-corda-transactions.png" width="1000" title="Click to zoom image in new tab/window" >}}


In our example you can see that the Buyer will initiate the ProposeFlow to create a Propose transaction.

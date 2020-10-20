---
title: Transactions, commands and signers
date: 2020-10-15T00:00:00+01:00
menu:
  cordapp-design-language:
    parent: cdl-ledger-evolution-view
    identifier: cdl-ledger-evolution-view-transactions-commands-signers
    weight: 20

tags:
- cdl
- cordapp design language
- ledger evolution
- cordapp diagram
---

# Transactions, Commands and Signers

We will represent transaction as a layered box:

{{< figure zoom="../resources/cdl-agreement-ledger-evolution-tx1-a.png" width="250" title="Click to zoom image in new tab/window" >}}

Where:
- The top box is a space for a label for the transaction, it doesn't correspond to a property of the transaction but is useful when describing a Ledger Evolution diagram.
- The middle green box is the command for the primary State.
- The bottom box contains the actual signers of the transaction.

If a transaction has additional commands relating to other Smart Contract operating in the transaction, then these are added as extra middle boxes.

{{< figure zoom="../resources/cdl-agreement-ledger-evolution-tx4-a.png" width="250" title="Click to zoom image in new tab/window" >}}

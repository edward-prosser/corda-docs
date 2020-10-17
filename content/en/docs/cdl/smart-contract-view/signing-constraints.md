---
title: Signing Constraints
date: 2020-10-15T00:00:00+01:00
menu:
  cordapp-design-language:
    parent: cdl-smart-contract-view
    identifier: cdl-smart-contract-view-signing-constraints
    weight: 110

tags:
- cdl
- cordapp design language
- smart contract
- cordapp diagram
---

# Signing Constraints

An important aspect of any CorDapp is the permissioning model, in other words who needs to sign which transactions. In Corda, the required signers are attached to the Transaction's Command. We mirror this in the CDL Smart contract view by including the require signer in brackets underneath the Command on the Path constraint.

{{< figure zoom="../resources/cdl-agreement-smart-contract-signing.png" width="1000" title="Click to zoom image in new tab/window" >}}

The signer is usually defined in terms of some property in the State. You would not usually specify that a particular Party, say PartyA must sign, instead you would specify that the party referenced in the seller property needs to sign, which may on some instances of the state happen to be PartyA.

It is also important to specify which state you are referencing for the property. Properties can change between the input state and the output state, you need to specify which.

In our example when performing a Repropose command, the output.proposer must sign, this may well be a different value from the input.proposer.

In some use cases signing constraints can get more complicated. A deliberately convoluted example might be: The buyer or seller must sign unless an escrow agent has presented a state of type X before the 3rd Tuesday in May. If this is the case, its best to use a separate call out to explain the constraint in more detail.

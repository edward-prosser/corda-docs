---
title: verifyPath()
date: 2020-10-15T00:00:00+01:00
menu:
  cordapp-design-language:
    parent: cdl-to-code
    identifier: cdl-to-code-verify-path
    weight: 130

tags:
- cdl
- cordapp design language
- contract to code
- cordapp diagram
---




# verifyPaths()

In the previous section we described how the `verifyPathConstraints()` function calls the `verifyPath()` function from `ContractUtils`. In this section we will describe how verifyPath() works. You can see the implementation of VerifyPaths in the cdl-example code repo. But for here, we can represent what it is doing using a diagram:

{{< figure zoom="../resources/cdl-contractutils-verifypath-simple.png" width="800" title="Click to zoom image in new tab/window" >}}

From the `LedgerTransaction` we can establish the `Path` in the transaction and the status of the Primary input state. From the status of the Primary input state we can establish the set of allowed `PathConstraints` for the status of that input state. The `verifyPath()` function then takes the Path and checks it against each `PathConstraint`. If it can find a `PathConstraint` which allows the transaction`Path` then it returns `true`, if not, it will return `false` which will cause the `verifyPathConstraints()` function to throw a verification error.

Looking in a bit more details we can see the properties in `Path` and `PathConstraint` respectively, together with the checks that each `PathConstraint` property applies to its corresponding `Path` property:

{{< figure zoom="../resources/cdl-contractutils-verifypath-with-properties.png" width="800" title="Click to zoom image in new tab/window" >}}


Then, for the full description, we need to add in the `additionalStates` checks in which each required `additionStatesConstraint` must be satisfied by at least one of the `additionalStates` in the transaction:

{{< figure zoom="../resources/cdl-contractutils-verifypath-full.png" width="800" title="Click to zoom image in new tab/window" >}}


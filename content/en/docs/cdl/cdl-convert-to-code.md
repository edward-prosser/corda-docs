---
title: CDL to Code
date: 2020-10-15T00:00:00+01:00
menu:
  cordapp-design-language:
    identifier: cdl-smart-contract-to-code
    weight: 30

tags:
- this
- that
- the other
---

# Converting CDL Smart Contracts To Code

The Previous sections on the CDL views aim to assist in the robust design of your CorDapps. However, they only give you the design, there still remains the task of converting those designs into executable code.

This section will discuss a structured and systematic approach to converting the CDL Smart Contract view diagrams into CorDapp Code.

The examples in this section are taken from the cdl-example CorDapp located in the Corda Open Source Github repo [here] (*** add link ***)

Important Caveats:
The cdl-example CorDapp is not an R3 product. It is only an illustration of one way a CDL Smart Contract view could be implemented. It has not gone through R3's normal rigorous Product and QA procedures and should not be relied on as production grade code.

# Keep it structured

The CDL Smart Contract view deliberate separates the design in to a set of concerns either to do with data structures or constraints over those data structures. We can show this diagrammatically:

{{< figure zoom="./resources/cdl-to-code-smart-contract-to-concerns.png" width="1000" title="Click to zoom image in new tab/window" >}}

To minimise the risks of making mistakes when implementing the Smart Contract we will consider each of the concerns separately. By narrowing the focus we can gain greater confidence that each concern is implemented correctly.

The CDL concerns will map to the following code structures:

{{< figure zoom="./resources/cdl-to-code-concerns-to-structures.png" width="650" title="Click to zoom image in new tab/window" >}}

Salient points are:

- CDL States map to the AgreementState
- Commands and the verify() functions are implemented in the Contract (as usual)
- The CDL constraints are implemented in the verify function, however...
- The verify() function is too complicate to manage in one function so it is broken up in to a series of sub-verify functions each one dealing with a different CDL constraint.
- The verification of Path constraints is more complicated than the other constraints, hence some of this has been moved out of the verifyPathConstraints() function and into ContractUtils.

For each of the sub-verify functions we will aim for a standard structure to implement that type of constraint. The closer we can get to a standard template with the specific details of the Smart Contract being configuration, the more reliable the implementation will become.

For example, the code for verifying Status constraints is as follows:

{{< tabs name="status-example" >}}
{{% tab name="kotlin" %}}
```kotlin
fun verifyStatusConstraints(tx: LedgerTransaction){

        val allStates = tx.inputsOfType<AgreementState>() + tx.outputsOfType<AgreementState>()

        for (s in allStates) {
            when(s.status){
                PROPOSED -> {
                    requireThat {
                        "When status is Proposed rejectionReason must be null" using (s.rejectionReason == null)
                        "When status is Rejected rejectedBy must be null" using (s.rejectedBy == null)
                    }
                }
                REJECTED -> {
                    requireThat {
                        "When status is Rejected rejectionReason must not be null" using (s.rejectionReason != null)
                        "When status is Rejected rejectedBy must not be null" using (s.rejectedBy != null)
                        "When the Status is Rejected rejectedBy must be the buyer or seller" using (listOf(s.buyer, s.seller).contains(s.rejectedBy))
                    }
                }
                AGREED -> {}
            }
        }
    }
```
{{% /tab %}}
{{< /tabs >}}

Which can be generalised to be applicable for any CorDapp which uses statuses:

{{< tabs name="generic-status" >}}
{{% tab name="kotlin" %}}
```kotlin
fun verifyStatusConstraints(tx: LedgerTransaction){

        val allStates = tx.inputsOfType<MyState>() + tx.outputsOfType<MyState>()

        for (s in allStates) {
            when(s.status){
                MY_STATUS_1 -> {
                    requireThat {
                        // Checks on states in status MY_STATUS_1
                    }
                }
                MY_STATUS_2 -> {
                    requireThat {
                        // Checks on states in status MY_STATUS_2
                    }
                }
            }
        }
    }
```
{{% /tab %}}
{{< /tabs >}}

The remaining sections will go through the implementation of each CDL concern

# States and Statuses



# Commands and splitting the verify


# Universal Constraints


# Linear Id constraints


# Command Constraints


# Signing Constraints


# Status Constraints


# Path Constraints










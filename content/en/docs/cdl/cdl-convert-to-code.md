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

The previous sections on the CDL views provide a structured way to represent the design of a CorDapp. However, they only give you the design, you still need to convert that designs into executable code.

This section will discuss a structured and systematic approach to converting the CDL Smart Contract diagrams into CorDapp Code.

The examples in this section are taken from the cdl-example CorDapp located in the Corda Open Source Github repo [here] (*** add link ***)

{{< note >}}
Important Caveat:
The cdl-example CorDapp is not an R3 product. It is only an illustration of one way a CDL Smart Contract view could be implemented. It has not gone through R3's normal rigorous Product and QA procedures and should not be relied on as production grade code.
{{< /note >}}


# Keep it structured

The CDL Smart Contract view deliberate separates the design in to a set of concerns either to do with data structures or constraints over those data structures. We can show this diagrammatically:

{{< figure zoom="./resources/cdl-to-code-smart-contract-to-concerns.png" width="1000" title="Click to zoom image in new tab/window" >}}

To minimise the risks of making mistakes when implementing the Smart Contract we will consider each of the concerns separately. By narrowing the focus we can gain greater confidence that each concern is implemented correctly.

In our Agreement example as implemented in the cdl-example CorDapp, the CDL concerns map to the following code structures:

{{< figure zoom="./resources/cdl-to-code-concerns-to-structures.png" width="650" title="Click to zoom image in new tab/window" >}}

Salient points are:

- CDL States map to the AgreementState
- Commands and the verify() functions are implemented in the Contract (as usual)
- The CDL constraints are implemented in the verify function, however...
- The verify() function is too complicate to manage in one function so it is broken up in to a series of sub-verify functions each one dealing with a different CDL constraint.
- The verification of Path constraints is more complicated than the other constraints, hence some of this has been moved out of the verifyPathConstraints() function and into ContractUtils.

For each of the sub-verify functions we will aim for a standard structure to implement that type of constraint. The closer we can get to a standard template with the specific details of the Smart Contract being akin to configuration, the more reliable the implementation will become.

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

The remaining sections will go through the implementation of each CDL concern.

# States and Statuses

A core element of the CDL Smart Contract view is that Corda States can be in different statuses. When in different statuses there are different restrictions on the form of the state and the transitions that state can make.

Contract States which require a status property should implement the StatusState interface. This requires val status: Status? to be implemented by the Contract State.

Note that status is nullable because we want to be able to represent no State as a status null. For example, when defining Paths allowed in transactions which have no input state we define the input status as null and map null -> allowed Paths.

In ContractUtils we define the interfaces for StatusState and Status:

{{< tabs name="status interface" >}}
{{% tab name="kotlin" %}}
```kotlin
/**
 * The StatusState interface should be implemented for all [ContractState]s that require a status field.
 *
 * [status] is nullable so that when there is no input or output state in a transaction, it can
 *
 */
interface StatusState: ContractState {
    val status: Status?
}

/**
 * Statuses are defined as enum classes in the StatusState which should implement this Status interface.
 */
@CordaSerializable
interface Status

```
{{% /tab %}}
{{< /tabs >}}


We then use these interfaces to define the AgreementState and the enum set of Statuses based on the CDL status state:

{{< figure zoom="./resources/cdl-agreement-smart-contract-statuses.png" width="1000" title="Click to zoom image in new tab/window" >}}

AgreementState.kt:

{{< tabs name="implement the state" >}}
{{% tab name="kotlin" %}}
```kotlin
@BelongsToContract(AgreementContract::class)
data class AgreementState(override val status: AgreementStatus,
                          val buyer: Party,
                          val seller: Party,
                          val goods: String,
                          val price: Amount<Currency>,
                          val proposer: Party,
                          val consenter: Party,
                          val rejectionReason: String? = null,
                          val rejectedBy: Party?= null,
                          override val participants: List<AbstractParty> = listOf(buyer, seller),
                          override val linearId: UniqueIdentifier = UniqueIdentifier()) : LinearState, StatusState

enum class AgreementStatus: Status {
    PROPOSED,
    REJECTED,
    AGREED
}

```
{{% /tab %}}
{{< /tabs >}}


# Commands

The Commands are implemented in the normal way as classes inside an interface class in the contract, each inheriting from CommandData. The list of Commands can be taken from the Path Arrows on the Smart Contract view.

AgreementContract.kt:

{{< tabs name="implement the commands" >}}
{{% tab name="kotlin" %}}
```kotlin
class AgreementContract : Contract {
    ...
    interface Commands : CommandData {
        class Propose : Commands
        class Repropose: Commands
        class Reject: Commands
        class Agree: Commands
        class Complete: Commands
    }
    ...
}
```
{{% /tab %}}
{{< /tabs >}}

# Splitting the Verify Function

As smart Contracts become more complicated, the risk of missing some important control grows. To reduce this risk the smart contract is split up into sub-verify functions which each deal with one of the types of constraints defined in CDL Smart Contract view. (With the exception of the blue Flow constraints which are not implemented in the Contract and are more notes on what the Flows should be doing.)


AgreementContract.kt:

{{< tabs name="split the verify" >}}
{{% tab name="kotlin" %}}
```kotlin
    override fun verify(tx: LedgerTransaction) {

        verifyPathConstraints(tx, AgreementState::class.java)
        verifyUniversalConstraints(tx)
        verifyStatusConstraints(tx)
        verifyLinearIDConstraints(tx)
        verifySigningConstraints(tx)
        verifyCommandConstraints(tx)
    }

```
{{% /tab %}}
{{< /tabs >}}

Later you may note that by splitting the verification into the sub-verify functions there is some duplication, eg multiple when statements on command.value, but the principle is that it is better to have some duplication if it allows better clarity and structure of the Smart Contract because this reduces the risk of making mistakes.

For all the sub-verify functions we pass in the LedgerTransaction, this is so each sub-verify has access to the whole resolved transaction. For verifyPathConstraints we also pass in the class of the AgreementState.

The following sections will consider the implementation of each constraint in turn.

# Universal Constraints

Universal Constraints are probably the most straight forward type of constraint, they apply to all states irrespective of what status they are in. The approach we take is to simply get all the states, then apply all the tests to each state.

AgreementContract.kt:

{{< tabs name="universal constraints" >}}
{{% tab name="kotlin" %}}
```kotlin
    fun verifyUniversalConstraints(tx: LedgerTransaction){

        val allStates = tx.inputsOfType<AgreementState>() + tx.outputsOfType<AgreementState>()

        for (s in allStates) {
            requireThat {
                "The buyer and seller must be different Parties." using (s.buyer != s.seller)
                "The proposer must be either the buyer or the seller." using (listOf(s.buyer, s.seller).contains(s.proposer))
                "The consenter must be either the buyer or the seller." using (listOf(s.buyer, s.seller).contains(s.consenter))
                "The consenter and proposer must be different Parties." using (s.consenter != s.proposer)
            }
        }
    }
```
{{% /tab %}}
{{< /tabs >}}


# Status Constraints

To verify the status constraints, again we need to get all the Primary states (i.e. the AgreementStates) in the contract, but then we apply a different set of tests based on the status of the state in question:


AgreementContract.kt:

{{< tabs name="status constraints" >}}
{{% tab name="kotlin" %}}
```kotlin
    fun verifyStatusConstraints(tx: LedgerTransaction){
        val allStates = tx.inputsOfType<AgreementState>() + tx.outputsOfType<AgreementState>()

        // Note, in kotlin non-nullable properties must be populated, hence only need to check the nullable properties of the AgreementState
        for (s in allStates) {

            when(s.status){
                PROPOSED -> {
                    requireThat {
                        "When status is Proposed rejectionReason must be null." using (s.rejectionReason == null)
                        "When status is Rejected rejectedBy must be null." using (s.rejectedBy == null)
                    }
                }
                REJECTED -> {
                    requireThat {
                        "When status is Rejected rejectionReason must not be null." using (s.rejectionReason != null)
                        "When status is Rejected rejectedBy must not be null." using (s.rejectedBy != null)
                        "When the Status is Rejected rejectedBy must be the buyer or seller." using (listOf(s.buyer, s.seller).contains(s.rejectedBy))
                    }
                }
                AGREED -> {}
            }
        }
    }
```
{{% /tab %}}
{{< /tabs >}}

AgreementContract.kt:

# Linear Id constraints

To verify LinearID constraints, the implementation has a few more complexities.

LinearIDs are used when want to represent a linear chain of evolving states on the ledger. There is an implicit assumption that to avoid branching of the linear chain, there will be at most one primary input state and one primary output state.

Later the Path constraints will explicitly state that cannot be more than one primary input or primary output state. However, we should program defensivly, so we add a pre check that wil throw a meaningful error if the transaction has more than one primary input or primary output state.

After the pre-check, we need to identify the situations where it is appropriate to check for matching LinearIds. This will be wherever there is both an primary input state and a primary output state and the Multiplicity is marked as '1: Matched'

From the diagram we can see that occurs for the Reject, Repropose and Agree commands:

{{< figure zoom="./resources/cdl-agreement-smart-contract-full.png" width="1000" title="Click to zoom image in new tab/window" >}}

Hence we will perform a switch ('when' in Kotlin) on the commands and apply the linear ID check for those commands.


AgreementContract.kt:

{{< tabs name="linearId constraints" >}}
{{% tab name="kotlin" %}}
```kotlin
    fun verifyLinearIDConstraints(tx: LedgerTransaction){

        val command = tx.commands.requireSingleCommand<AgreementContract.Commands>()
        val inputStates = tx.inputsOfType<AgreementState>()
        val outputStates = tx.outputsOfType<AgreementState>()

        // Assume that if using LinearID we want a maximum of one Primary input state and a maximum one Primary output state
        // This is a guard which shouldn't be triggered because the Path constraints should have already ensured there is
        // a maximum of one Primary input state and a maximum one Primary output state
        requireThat{
            "When using LinearStates there should be a maximum of one Primary input state." using (inputStates.size <= 1)
            "When using LinearStates there should be a maximum of one Primary output state." using (outputStates.size <= 1)
        }

        val inputState = inputStates.singleOrNull()
        val outputState = outputStates.singleOrNull()

        val commandName = command.value::class.java.simpleName
        when (command.value){
            is Commands.Reject,
            is Commands.Repropose,
            is Commands.Agree-> {
                requireThat {"When the Command is $commandName the LinearID must not change." using(inputState?.linearId == outputState?.linearId)}
            }
        }
    }
```
{{% /tab %}}
{{< /tabs >}}

# Command Constraints
{{< tabs name="command constraints" >}}
{{% tab name="kotlin" %}}
```kotlin

```
{{% /tab %}}
{{< /tabs >}}


# Signing Constraints
{{< tabs name="signing constraints" >}}
{{% tab name="kotlin" %}}
```kotlin

```
{{% /tab %}}
{{< /tabs >}}


# Path Constraints










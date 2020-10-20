---
title: CDL to code
date: 2020-10-15T00:00:00+01:00
menu:
  cordapp-design-language:
    identifier: cdl-to-code
    weight: 30

tags:
- cdl
- cordapp design language
- contract to code
- cordapp diagram
---

# Converting CDL Smart Contracts To Code

The previous sections on the CDL views provide a structured way to represent the design of a CorDapp. However, they only give you the design, you still need to convert that designs into executable code.

This section will discuss a structured and systematic approach to converting the CDL Smart Contract diagrams into CorDapp Code.

The examples in this section are taken from the cdl-example CorDapp located in the Corda Open Source Github repo [here] (*** add link ***)

{{< note >}}
Important Caveat:
The cdl-example CorDapp is not an R3 product. It is only an illustration of one way a CDL Smart Contract view could be implemented. It has not gone through R3's normal rigorous Product and QA procedures and should not be relied on as production grade code.
{{< /note >}}

At present the examples are only available in Kotlin. However, the principles being employed will translate to Java and the functions provided in ContractUtils.kt will still be able to be used by Contracts written in Java.







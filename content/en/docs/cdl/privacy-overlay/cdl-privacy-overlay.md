---
title: Privacy Overlay
date: 2020-10-15T00:00:00+01:00
menu:
  cordapp-design-language:
    parent: cdl-views
    identifier: cdl-privacy-overlay
    weight: 16

tags:
- cdl
- cordapp design language
- ledger evolution
- cordapp diagram
---

# The Privacy Overlay

Privacy is a key consideration when designing CorDapps. In most use cases Parties on the ledger will want to keep their Corda states representing private deals, asset, permission tokens etc.. private. There's normally two aspects to this:

- Keeping states private from the network operator, this avoids one (centralised) node holding all information about the activity network.
- Keeping states private from other Parties on the network who are not entitled to see the states, ie they we're part of the deal.

This section will show how we can use a Privacy Overlay over the Ledger Evolution view to highlight the privacy characteristics of a CorDapp design.

To illustrate the Privacy Overlay we will add a Billing mechanism into the Agreement example from the Smart Contract and Ledger Evolution sections.








---
title: Testing
description: Create automated proof that things work
---

Templates and patterns for practical creation of automated scenario-testing

---

## Why create automated tests?

Writing extra code for tests can sometimes seem like a distraction.  

While most applications have their own UI, ***testing by clicking things only demonstrates things working today***.  And it always gets slower and harder as your application grows.

With an efficient testing environment, you can easily make small scripts proving that each required behavior is running properly.  ***You can keep quality high while developing your contract suite further, with more confidence***.

Every activity in your Stellar constellation can have clear linkage between requirements, utility functions, and on-chain behavior.  

Well-tested code is more easily audited, making for ***faster and easier certification***.  

## Introducing the Helios Testing Context

Stellar contracts provides a HeliosTestingContext object, which integrates smoothly with `vitest`, providing strongly-typed helper functions to **make it easy to automate user-stories**

### Built on Helios and `vitest`

Vitest is a beautiful iteration on the semantics of the Jest testing framework, and its predecessors in the open source community.

Fast startup, simple setup and Typescript integration create an easy environment for you to build high-functioning constellations of contracts and make your dApp great.

### Simulate on-chain activities

Helios provides a Network emulator that runs all the important "on-chain" checks in a simple sandbox, where we can simulate all the critical expectations of smart-contracts without extra operational complexity.  

Thanks to the network emulator provided by the Helios layer, we can run our user scenarios and check "on-chain" that the transactions worked and produced the expected results.  ***No need to run a testnet node or wait around for confirmations***.

### Check on-chain results

Our helper functions make it simple to query into the emulated block-chain, ensuring that money, tokens, contracts and people are doing all the right things and getting all the right results.

### Cover each step of your user journey

Integrating Stellar Contracts' ***requirements protocol*** and unit tests helps you create clear inventories of user-journeys, behaviors, expected activities, and guard-rails.  

These inventories serve product managers, project developers and contract auditors equally, reducing cost of certification and iterative development to close possible gaps identified by auditors.

They also **build your confidence in the correctness of your dApp**, including the completeness and safety of your contracts.

### Trigger every representative case, without testing every possible scenario

When you build using constellations of granular contracts, each contributing script can only have a limited number of cases.  It's common for each of these cases to be easily tested, in relative isolation.

Constellations (aka ***compositions***) of contract scripts can have an ***unlimited number of possible scenarios*** when combined in different ways. That's the beauty of lambda calculus.

***Automated tests allow you to have confidence in every element of your contracts***, without needing to test thousands of the possible combined scenarios.

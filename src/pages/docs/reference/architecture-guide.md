---
title: Architecture guide
description: Patterns and guidance for architectural problems
---

A gentle guide to great structures that make smart contracts work for you

---

## Intro: A new model, with different capabilities?

The Plutus data and execution model, with Datums and Redeemers and Reference Contracts and Minting Policy Hashes and pure functional languages and Collateral has little that's familiar to web-based software developers.

Our architecture guide will connect the dots so you can design sensible structures for great smart contracts to serve your powerful new dApps.

### New Tricks for Cardano contracts

Cardano contracts are immutable.  We provide patterns for making them evolve and grow.

Cardano contracts can't autonomously react to new activity on-chain.  We'll show you how to get all the on-chain effects your dApp wants, without operating chain-transaction monitors.

###  New Tools for Cardano developers

As Cardano dApp architects, we are seeing patterns of sweet technical tricks involving UTxOs, native-tokens, and more, that enable sophisticated on-chain effects with very little server involvement.

Read on to learn about the patterns we've been discovering for structuring on-chain dApps and the offchain Typescript code that interacts with them.

## Top Patterns for Stellar Contracts

###  Pattern 1: the UUT

Sometimes called a Thread token, a UUT has technical similarity to the mechanism that enables NFTs.  They're  a simple way to link two contracts with a semantic binding.  

{% quick-links %}

{% callout title="Capo mints a UUT" %}
    ...delegates authority and includes the delegate in related txns
{% /callout %}

{% callout title="Delegate holds UUT" icon="🪙" iconSize="180%" href="" %}
    ... enforcing its policy when the UUT is used in a txn 
{% /callout %}

{% /quick-links %}


**UUTs link separate scripts**, enabling an implicit collaboration between them.  They can easily combine with inline Datum elements as configuration details affecting the collaboration.

{% callout title="UUT's home and lifetime" %}
  A UUT should typically last as long as its delegate, and be stored in that delegate's validator script address.  
  
  When a delegate is no longer needed, the UUT can be retired, burning it so that it no longer exists in any UTxO.
{% /callout %}

### Pattern 2: the Activity

**Activities** are specific actions done in relation to a UUT or to a value-bearing UTxO being spent from a contract.  They often ***connect naturally to user-stories in application requirements***.

You can use Redeemer data of the right type to provide more specificity around each activity.

In transactions involving multiple collaborating scripts, the Activites (and their redeemers) from each contributing script are combined: 

  * Initiate a `mkTxnCreateFundingProposal` transaction,
  * ... include a `mintingProjectOwnerToken` activity, 
  * ... and add a `provingMembership` activity 
  
Through composition and collaboration, three different contract scripts each do small parts and ***get things done together***.

Our naming guidelines give designers and developers a clear, **intuitive convention to boost clarity for everyone** involved with your Stellar (off-chain and on-chain) code.

### Pattern 3: the Capo

A leader contract that might itself do very little beyond being a **hub for coordinating between other contracts** under its umbrella of responsibility, as you've defined it for your application.  Its off-chain part might contain a number of `mkTxn...` entry-points to serve user-stories from your product requirements.

Pairing it with a minting script can give you an easy source of creating UUTs to use in delegation patterns.

### Pattern 4: the Delegate

{% callout %}
A delegate contract plugs into a leader contract using a UUT stored at the delegate's script-address.

The leader remembers the details, in a UTxO owned by the leader; the details point at that delegate address and the UUT, creating a firm linkage between the contracts.

_The delegate serves a role for the leader/Capo, allowing responsibilities to be divided._
{% /callout %}

UUT-linked delegates can have broad independent authority or be tightly constrained.  They can be sent to contracts, sent to people, traded to other contracts ...

... or be narrowly scoped, tying them to specific accounts or contracts.  The **delegate can then approve transactions, when they're done in proper & prescribed ways** (or, reject them, otherwise).

As a contract designer, you can also choose variations on the theme to get the results you need.

### Pattern 5: Off-chain collaboration and partial transactions

Off-chain dApps, with or without a corresponding application back-end, have a key role to play in creation of the transactions needed to make progress on any kind of smart contracts.

With a constellation of on-chain contract scripts comes a corresponding constellation of off-chain contract components.  Each one contributes pieces of transaction-building **responsibilities that combine to form a beautifully coordinated result**.  

>                _...a plumber, an electrician, a carpenter and a rabbi walk into a bar..._

In this seed of a humorous tale, there is also intuition to be found about collaborating smart-contract components.

#### An example constellation of collaborating scripts

This example might serve as part of a project-management system:

{% quick-links %}

{% callout title="Capo"  href=""%}
... integrates the other scripts
{% /callout %}

{% callout icon="📜" iconSize="180%"  title="Reputation Policy" %}
     ... adds proof of skill / reputation required to do work and earn a reward
{% /callout %}

{% callout icon="🪙" iconSize="180%" title="Profit-sharing"  %}
... routes a portion of transaction value to your treasury and to contributors
{% /callout %}

{% callout icon="📔" iconSize="180%" title="Process state-machine"%}
... moves a project into the right state when its rules are satisfied
{% /callout %}

{% /quick-links %}

#### Every script in the constellation does its own part

A leader contract's transaction-builder for a given activity typically triggers specific related activites served by delegates.  Their off-chain components typically do things like:

  * checking known on-chain addresses to ***find certain tokens, values, and UUTs***
  * ***starting a transaction***, or joining one already in progress
  * contributing a value, or a token or ***UUT into a transaction***, computing correct values, and ensuring the right contract-scripts are triggered
    - these can call into "on-chain" Helios code to ensure key business logic has a single point of origination
  * ***returning a UUT*** to its point of origin so it can be used next time
  * failing to build a transaction if the current user doesn't have access to the key inputs for the needed transaction

#### Every involved contract script can contribute elements such as these

... in ways people find needful for their transactions.  A **transaction context** ***provides a fluent container*** for transaction-building, and it integrates partial-transactions contributed by separate scripts in a constellation. 

This **collaboration and composition** pattern enables sets of contracts (their off-chain aspects) to start, embellish, augment and delegate transaction-building policy.  Together, ***they form the transactions  needed for your dApp to make progress and fulfill its purpose***.

#### Collaborative on-chain validation

In the same way collaborating off-chain components work together, their on-chain parts also collaborate, each validating their important parts and guarding against wrongly-built transactions.

{% callout title="on-chain vs off-chain" %}
    Off-chain dApp code makes the transactions people want.

    On-chain contract scripts guard against txns people DON'T WANT.
{% /callout %}


#### Results

Contracts built with this pattern are simpler, easier to review, more auditable, and more easily certified.  Their elements are flexible for great reusability and customization, and deliver ***safer, more predictable results***.

>   _so the rabbi says, 'Please, God, make sure the electrician isn't working on the same day as the plumber'_

## More valuable patterns

### Pattern 6: Writing Automated Tests

Meanwhile, each collaborator in a constellation can have its own automated tests proving it does well all the right behavior it needs to be of good service, and none bad.

{% callout %}
                _Test early, test often_
{% /callout %}

Our HeliosTestingContext provides some useful elements and suggests some context-specific patterns to make test-automation easier and more reliable.

### Pattern 7: Well-Maintained Requirements

A well-regulated expression of product requirements, with clear linkage between customer goals and product design, between product design and technical mechanisms, can be important factors in a project's effectiveness.

Connecting test-scenarios with technical requirement adds yet another layer of clarity for auditors.  We can help with that too.


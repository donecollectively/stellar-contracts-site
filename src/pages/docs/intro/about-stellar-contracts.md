---
title: About Stellar Contracts
description: Constellations of single-pointed contract scripts to form a beautiful picture within your dApp
---

Constellations of single-pointed contract scripts to form a beautiful picture within your dApp

---

## Tiny stars

Plutus smart contracts become better when they're simpler and do less.

### ??? I'm seeing stars !!!

The first-generation Plutus language, offered from the Haskell environment, offered some on-chain fundamentals, strongly designed to serve the future.  

We've all been working to figure out how to use the capabilties of on-chain plutus to build better dApps, but they're much different than anything developers have normally been using in the regular world.  Redeemers and Datums, minUtxos and coin-selection... oh my!

Stellar Contracts lets developers shift away from "How do transactions have to be built?".  Our utility methods and pattern library provide good building blocks, so you can switch to a new question:

### Which activities and results do people need from your application?

We help product development teams be focused on those results.

In this example, the mkTxn pattern constructs a transaction for a particular user story in an application.
A person is receiving an escrow payment. 

```ts
    //! receives an escrow payment for the given escrow-id
    //  by constructing a transaction having that meaning
    @txn
    async mkTxnReceiveEscrowPayment(
        { escrowId }: EscrowIdDatum,
        tcx: StellarTxnContext = new StellarTxnContext()
    ) {
        //! includes an escrow-id token, which can only be spent
        //   ... by a person having that token
        return this.txnIncludeEscrowIdUUT(tcx). then(hasTheUUT => {
            //! adds the payout details
            // This is done by delegating to a SEPARATE tiny contract 
            //  ... that takes responsibility for paying out 
            //  ... to the right party as indicated by that governor's policy.
            return this.governer.txnAddEscrowPayee(
                tcx,
                this.address,
                this.escrowPolicy
            );
        })
    }
```

**Governor**?  The 'governer' policy here is participating in the transaction, and it can provide any custom implementation of payout logic.

Separate points of functionality in **closely collaborating mini-contracts** makes for great flexibility, enabling one contract to serve dozens of different use-cases, simply by connecting loosely to various `governor` protocols.

Combining 3 or more contracts is as easy as combining two.

## A deeper look

Let's dig into some of the most common patterns dApp developers can use to make great Web3 software.

### Shifting your Engineering Attention



The transaction-building model promoted by Stellar contracts encourages the creation of small helper methods that take care of the most useful patterns that can make great functionality out of just a few moving parts

  * For **each primary kind of transaction people will do in your application**, use the 
    **mkTxnXxxxYyyyy()** pattern (like  `mkTxnReceiveEscrowPayment()` above)

        These are the main kinds of function your application code will call when you need to record progress, authorize a change, or transfer funds during user interaction.
  * For **connecting scripts together**, get onboard with the creation of **unique utility tokens** 
  
     UUTs provide strong links between separate smart contract scripts.  You can think of these as a special, simplified kind of NFT that aren't traded, and nobody ever looks at.  
     
     UUTs are the scaffolding needed to connect different contract scripts together.  
     
  * Every "application-backend", whether browser- or server-based, must **locate the coins and tokens** that contribute to the correct semantic mechanisms for making sure their contracts do what they need to do.

      In Stellar Contracts, we promote easy design patterns for finding those UTxO's, and for adding them into a transaction **in ways fitting your application semantics**.  Link: more on transaction-building-blocks

### Multiple Collaborating Software Modules

Just as separate on-chain scripts can combine to form effective constellations of on-chain capabilities, we can construct off-chain modules, each written in Javascript / Typescript, that do certain aspects of the transaction.

The `governer` in the example above assumes **responsiblity for paying the right parties the right amounts**, delegated from the short function that gets the "receive payment" transaction built.  

It references the governer's purpose and **pushes the fine details of achieving it** into separate responsibilities of the governor.

These patterns enable each software module to do one primary purpose and do that thing simply and clearly.

## More on Transaction Building-Blocks

At the level of "fine details", every application back-end has to actually construct the different parts of every transaction.  Each collaborating component of a Stellar constellation serves its purpose, and when they do, it's quite frequent for them to do such things as:

  **txnInclude...()** - helper methods that add **unique utility tokens** or any other transaction details into a transaction that's being created.
  * **mustFindSssssOmething()** - helper methods used to locate unique utility tokens (**UUTs**) The names you give them can be descriptive of the different script components in your application.
  
  ### Activities: beyond the redeemer

  In Stellar Contracts, we reduce the visibility of the thing Plutus calls `redeemers`.  Instead, we promote the pattern of **Activities**, which have similarities to server API's.  

We encourage Activities to use active verbs, like authorizingPayment, withdrawingRoyalties, or revokingAuthorization.  By using these sorts of names, we find it creates big opportunities for contextualing details (the Redeemer data) behind these activity names.

Redeemers for these examples might identify payment-id, recipients for royalties, or the person whose authorization is being revoked.  **Activities are the entry-point for people** to use "redeemer" details in their transactions.

## Learn more...

{% quick-links %}

{% quick-link title="What is a Constellation in Stellar?" icon="lightbulb" href="/docs/reference/StellarContract" description="Understand how Plutus scripts combine to form a Stellar contract" /%}

{% quick-link title="Getting Started: Installing" icon="installation" href="/docs/starting/installation" description="How to start using Stellar Contracts today" /%}

{% quick-link title="Learning Stellar Together" icon="presets" href="/docs/contributing/join-to-learn" description="Join our interactive learning program and learn to build richer on-chain capabilities with less code" /%}

{% quick-link title="Stellar Product Cooperative" icon="theming" href="/docs/contributing/join-to-earn" description="Become a member and part owner of our cooperative venture.  Get credit for every contribution and participate in profit-sharing." /%}

{% /quick-links %}
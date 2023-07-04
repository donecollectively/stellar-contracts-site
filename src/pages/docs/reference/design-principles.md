---
title: Design principles
description: How to make contracts Stellar on Cardano
---

How to make Cardano contracts Stellar

---

## How we Designed Stellar
### Principle: Start with a Great On-Chain Language

Helios is a beautiful work of language design, with some simple but powerful essential capabilities and a close fit for the Cardano on-chain environment.

### Principle: Cater to Native Speakers

On Web3, Javascript and Typescript are the _lingua franca_.  

We say "roll with it".  Exposing Typescript types from Helios contracts makes it easy to work with on-chain details from the off-chain domain, right there at the border between Web and Web3.

### Principle: Code should make sense to a reader

The interfaces we give developers to get things done should be fit to developers and user intent, and be easily understood by someone having only an understanding of user-stories, and empathy for the end user.

#### Terminology should evoke a sense of purpose for readers

Names should carry intuition, simply from reading the name.  This example tells its own story:
```jsx
<button onClick={() => {
    this.marketplace.mkTxnReceiveNFT()
}}>Receive your Collectible</button>
```

***Consistent naming conventions*** are also used inside functions in your Stellar dApp,  like this `mkTxnReceiveNFT` example, to evoke clear purpose deeply throughout a dApp's code.

{% callout %}
_This approach makes application code very clear and "read/write" instead of "write-only"._
{%/callout %}

#### Consistent patterns used in a domain model should have consistent names

Another example that tells its own story about creating a type of Datum:
```js
mkDatumNftTradingTerms({ platformCommission, resaleRoyalty, minimumBid })
```
#### Programmers should always be able to code fluently

When a developer is about to hook a "Send Payment" button up to the blockchain, the lines of code they write should be ***brief and clearly descriptive of purpose***.

As they continue implementing different details of their apps and transactions, this trend should continue, with ***application-domain helper functions*** developers can fluently find and use.

The patterns of naming convention provide strong hints to name things in ways that promote fluent coding.  A developer may not remember the full  name of a `mkDatum...` or `mustFind...` method, but autocomplete based on those conventions gives them great leverage.

### Naming conventions in Stellar

  * **Activities** get active verbs like _`receivingNFT`_, _`mintingNamedToken`_, or _`suggestingCharterChange`_.  
  * **mkTxn**XxYyy() methods start the creation of a transaction, and the purpose of that transaction is found in the rest of its name.
  * **mkDatum**XxYyy() methods are used for generating specific types of Datum structures for use in on-chain transactions.  They can express ***configuration*** or ***state management***  semantics.
  * **mustFind**XxxYy**Utxo**() methods are used for the pattern of finding some particular UTxO - often one with a particular UUT.  Frequently used in transaction-creation partials.

{% callout %}
_These four patterns of **naming things** cover most of the common interactions dApps will have from UI code, at the bridge to their Helios contracts._

{% /callout %}

There are some additional naming conventions that are equally important, but may be seen less frequently wtihin UI code.  They pave they way for great architecture, collaboration between contract-scripts, flexibility and reusability.  

  * **txn**XxYyy() aka "partials" - these methods add details to a transaction, when those details are not fit to stand as a separate txn.  Partials can't stand on their own; they enable or guard other transactions.  Great for collaborator contracts.
  * **related**XxYyy() - these methods exist to express relationships to delegates (i.e. other on-chain/off-chain scripts). Functions using this pattern will likely include a call to `addScriptWithParams`, referencing a Stellar Contract subclass fitting the meaning found in the "XxYyy" part.


## How to Build Your Contracts

### Be familiar with the Primary Patterns

Our number one piece of advice for contract developers is to make a daily practice of writing and/or using the [5 fundamental patterns](/docs/reference/architecture-guide#top-patterns-for-stellar-contracts) mentioned in the Architecture Guide

#### Use the patterns

It might seem obvious, but it's also pretty easy to forget about the usefulness of composability and delegates.  Practicing use of the patterns helps - a bunch!

_The more we can think of multiple, granular scripts as simply **lightweight, modular elements of a single and integrated whole**, the easier it can be to add value to our dApps through modules instead of growing a monolithic contract script._

### Practice different ways of delegating

Delegate UUTs can be used in a few different ways.  You can allow UUTs to be transferred while retaining authority semantics, or you can enforce that the UUT can only be valid when provided by a specific policy-script.  

### Practice mockability

Construct helper functions that can easily be mocked out for testing purposes, producing a transaction that fails in an expected way.

Such an expected failure (aka a **negative test**) demonstrates the importance of the item missing from a txn - such as a UUT that is needed in a certain situation.  Putting it explicit in the automated test plan actively guards your codebase from changes that would produce the wrong result.

### Habitually test-drive new contract code

Follow these sequences to build tests and make new contract functionality:

  * Write a requirement or pick from an existing TODO for a reqt.
  * Add a unit test matching the `mech` entry
  * Write a `mkTxn...` call, or a test-helper method that does `mkTxn...`, or add a call to such a helper in the test
  * Make the test pass
  * Run your full test-suite again and correct any problems found

This is more a process than a principle.  Practice it to get more familiar, then to get fluent, then fast and confident.  You're going to go far!

{% callout %} 
     Doing these practices will improve the design of your contract components.
     
     Mockable transaction-helpers aren't just good for tests.  They tend to improve code clarity and quality too.
{% /callout %} 

A full automated test suite also serves auditing and certification process.

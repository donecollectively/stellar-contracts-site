---
title: Using StellarContract
description: A practical intro
---

The StellarContract class is a Typescript module providing a facade for both on-chain and off-chain aspects of your smart contract.

---
{% quick-links %}

{%callout type="stellar" title="Constellation?" %}
_Tiny points of light_ {%br /%}
_coming together_{%br /%}
_in a delicate dance_

**Read more below**
{%/callout%}

{% quick-link title="Getting Started: Installing" icon="installation" href="/docs/starting/installation" description="How to start using Stellar Contracts today" /%}

{% quick-link title="Architecture guide" icon="presets" href="/docs/reference/architecture-guide" description="Designing with granular contract scripts to build richer on-chain capabilites with less code" /%}

{% /quick-links %}



Poetry aside, a Stellar Constellation combines multiple small on-chain Plutus scripts with Typescript classes into a ***cohesive suite of designed on-chain and off-chain behavior for your dApp***.

It includes (value-free) native tokens for their uniqueness, and for their utility in flexibly connecting between on-chain scripts.  

And it includes ***all the patterns*** of finding the right native tokens and other assets, constructing transactions in reliable ways, so you can ***execute the right transactions on-chain*** for your dApp.

## Structure of a StellarContract

### Start by Subclassing

You can start by defining a subclass of StellarContract.  This example defines some of the most essential elements, just to give you a concrete starting-point:

```js
import { Address } from "@hyperionbt/helios";
import { StellarContract } from "../lib/StellarContract.js";
import contract from "./Escrow.hl";

export type EscrowParams = {
    receiverAddress: Address;
    administratorAddress: Address;
};

export class EscrowContract extends StellarContract<EscrowParams> {
    contractSource() {
        return contract;
    }
}
```

This code snippet defines a top-level contract (the "Capo" pattern).  We'll build on it further below.

{% callout %}
Need help with the `import contract ...` line?  [See below](#footnote-importing-helios-source).
{% /callout %}

### Defined Activities 

**Activities** involve spending a contract UTxO while performing specific kinds of validations that are fit to the purpose of that spending.  In short, they're ***on-chain actions done by dApps and people using them***.

A ***redeemer*** data-structure identifies which activity and which details are needed to get that activity done. In Stellar contracts, each activity is marked with `@redeem`, and it is ***typically named with "ing" phrasing***, reinforcing the verb nature of activities.

### Defined Datum types

Data stored in a Stellar contract can represent that contract's overall state, or an element of ***contract state connected to one item that the contract has responsibility for***.  

Helios and Stellar will make Datums defined in the contract available to Typescript, so off-chain code can read and write Datum variants to interact with on-chain data.

### Defined UUTs, named Utxo's, and finders for them

Creating application-level semantics is one of the main themes for named-UUTs and Named-utxos.  They're built out of low-level mechanisms, but you can add ***names and purposes, creating meaning in your dApp***.

`mustFind...` is a typical convention for off-chain helper functions that locate named UTxO's, whereever they may be.  They might look in a particular contract address, or they might look in the current user's wallet.

Named UUTs and Utxo's are typically used within transaction-building helpers.

### Defined delegates

{% callout title="Delegate" %}
A chosen representative for a specific task or role
{% /callout %}

Having identified separate pieces of responsibility that can add flexibility and extensibility to your main contract, defining each as a named delegate gives it a conceptually-named role it can play.

Just as different stage-actors may play Macbeth with their own flair, different delegates serving in a role can serve your main contract in ways unique to each.

`addScriptWithParams()` can be used to connect delegate scripts to your contract.

### Transaction factories and partials

`mkTxnXxYyy()` (and `txnXxYyy()`) methods create (and contribute to) a transaction before it is finalized, signed and submitted to the chain.

These methods are directly linked with Activities, as well as to application-level behaviors of people using the contracts' capabilities.  See below for examples of these.

## More About Contract classes

Each parameterized subclass of StellarContract already gives a working constructor that requires appropriate setup details (`receiverAddress` and `administratorAddress`, in the example).  

It parses the contract source and recognizes **Activities** (with their corresponding Redeemer details) and types of **Datum** defined in the contract.

#### Composing Stellar Contracts

In our example, we're going to express a collaborating contract called an EscrowPolicy.  Defining this helper method on the main EscrowContract class gives us a clear way to access a specific **EscrowPolicyContract** from within the main Escrow contract class.

```js
getEscrowPolicy(): EscrowPolicyContract {
    if (this.escrowPolicy) return this.escrowPolicy;
    const {receiverAddress} = this.params

    return (this.escrowPolicy = this.addScriptWithParams(
        EscrowPolicyContract, {
            receiverAddress
        }
    ));
}
```
Typescript validates that the params are matching the EscrowPolicy's configuration parameters, and it even helps us with autocomplete.

{% callout title="Behind the curtain" %}
We haven't explicitly shown the `EscrowPolicyContract` class here.  

It's simply a separate `StellarContract` subclass, with its own type of parameters.
{% /callout %}


### More elements in Stellar Contracts

#### Requirements

Encodes the needs and expections people will get from your constellation of software components.  We'll share more information about how requirements fit into a larger context around formal methods, auditability, and zero-knowledge proofs, as time and funding allow.

{% callout title="Get professional help" icon="💼" iconSize="180%" %}
_For a consultation with us about your requirements, [join us on Discord](https://discord.gg/UkUB3HZRcR) and submit a private request there._

    TODO @Piotr drop a Bonfire button in here 
{% /callout %}

#### Minting helper

Because creating UUTs for connected delegates is so important, we made a minter always available for use in your contract constellation.

In the next example, we use the Helios API and a few helpers from Stellar to make a complete transaction.  It includes a minted UUT.

{% callout title="Minting a UUT for a new Delegate" %}

A UUT can be created based on any unique input:

```js    
    firstUtxo = txin[0];  
    const uut = this.mkUUTFromSeed(firstUtxo, "policy");
```
{% /callout %}

In this pattern, we use a convention: the identity of a UTxO being spent will always give us a uniqueness guarantee for the new UUT.  Any input will do fine - even spare change, if your contract creation doesn't need any significant value deposited.

### Building an entire Txn with a delegation helper and UUT

This example shows the creation stage of an Escrow contract instance:

```js
async mkTxnCreateEscrow(
    escrowAmount: Value,
    policy: EscrowPolicy
    tcx = new StellarTransactionContext()
) {
    const utxin = await this.mustFindValueInWallet(escrowAmount);
    tcx.addInputs(utxIn);
    firstUtxo = utxin[0];
    const escrowId = this.mkUUTfromSeed(firstUtxo, "policy");
    const escrowConfig = this.mkDatumEscrowInstance(uut, this.getEscrowPolicy().address);

    // invoke a delegate, wiring them into the Capo contract via the UUT
    this.getEscrowPolicy().txnNewEscrowInstance(tcx, uut, this.address);
    //  -> the UUT goes to the delegate

    //  ...  and a reference to that UUT is stored in the local contract, with an
    //        "EscrowInstance" Datum.
    tcx.addOutput(
        this.address, 
        this.mkValueRef(uut), 
        escrowConfig
    )
    return this.tcx;
}
```
In this transaction factory, note:

  * A UUT is created for the escrow instance
  * A related EscrowPolicy contract receives the UUT
  * A Datum (an EscrowInstance) is included in the contract 
  * The EscrowInstance has a reference to the UUT and the address of the escrow policy script


{% callout title="TODO before broad distribution" %}
TODO: finish implementing mkUutFromSeed based on existing 'charter' pattern

TODO: implement mkValueRef() for simple convertion from uut to the right Value type
{% /callout %}

You can override the default minter and use it for more than just UUTs.


## Footnote: Importing Helios source

{% callout title="Hint: Importing Helios on-chain source code" %}
    Use the rollup `string` plugin with vite / vitest if you have trouble with the `import contract ...` line

```js
// vite.config.js
import { string } from "rollup-plugin-string";
    [...]
    plugins: [
        string({
            include: "**/*.hl",
        })  
    ],
 ```
{% /callout %}

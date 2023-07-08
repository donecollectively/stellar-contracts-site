---
title: Using StellarContract
description: A practical intro
---

The StellarContract class is a Typescript module providing a facade for both on-chain and off-chain aspects of your smart contract.

---
{% quick-links %}

{% quick-link title="Architecture guide" icon="presets" href="/docs/reference/architecture-guide" description="Designing with granular contract scripts to build richer on-chain capabilites with less code" /%}

{% quick-link title="Getting Started: Installing" icon="installation" href="/docs/starting/installation" description="How to start using Stellar Contracts today" /%}
{% /quick-links %}
{%callout type="stellar" title="A Constellation" %}
_Tiny points of light_ {%br /%}
_drawn together, silver threads_{%br /%}
_An elegant dance_
{%/callout%}



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

**Activities** involve spending a contract UTxO while performing specific kinds of validations that are fit to the purpose of that spending.  In short, they're ***on-chain actions done by dApps and people using them***.  Each activity normally comes with a transaction-building entry-point (`@txn`) and/or partial helper (`@Activity.partialTxn`).

A ***redeemer*** data-structure identifies which activity and which details are needed to get that activity done. In Stellar contracts, each activity is marked with `@Activity.redeemer`, and it is ***typically named with "ing" phrasing***, reinforcing the verb nature of activities.

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

### Capo pattern and Minting helper

Because creating UUTs for connected delegates is such a valuable pattern, we provide a class that automatically includes a minter

To use the UUT pattern, use the `Capo` class instead of inheriting from `StellarContract`:

{% callout title="Minting a UUT for a new Delegate" %}

```js    
import {Capo, Activity} from "@donecollectively/stellar-contracts";

class SomeEscrowContract extends Capo {
    // ...

    @Activity.partialTxn
    txnCreatingNewInstance(tcx: StellarTxnContext) {
        const uut = this.txnCreatingUUT(tcx, "escrowPolicy");
        this.getEscrowPolicy().txnCreatingPolicy(tcx, uut)
    }
}
```
{% /callout %}

In this example, a new "escrowPolicy" token is being created.  It's then passed into a partial helper on a related policy contract, which we can expect creates a Datum and deposits the UUT.

The actual name of the new token will start with its "escrowPolicy" purpose, followed by a dot, plus 16 characters of bech32-encoded uniqueness.  

It's fine to override the simple default minter, and use it for more than just UUTs.  

In the next example, we use the Helios API and a few helpers from Stellar to make a complete transaction.  It includes a minted UUT.

### Building an entire Txn with a delegation helper and UUT

This example shows the creation stage of an Escrow contract instance:

```js
async mkTxnCreateEscrow(
    escrowAmount: Value,
    policy: EscrowPolicy
    tcx = new StellarTransactionContext()
) {
    const escrowIdUUT = this.txnCreatingUUT(tcx, firstUtxo, "policy");

    const escrowPolicy = this.getEscrowPolicy(); // delegate address

    // 👁️ invoke a delegate, wiring them into the Capo contract via the UUT
    escrowPolicy.txnCreatingPolicy(tcx, escrowIdUUT, this.address);
    // 👉  UUT is sent into the delegate here  ☝️

    //  A reference to that UUT is included in the escrow-config,
    //  ... using the EscrowInstance Datum   👇
    const escrowConfig = this.mkDatumEscrowInstance(
        escrowIdUUT, escrowPolicy.address);

    // Finally, the escrow amount is stored in the escrow contract, 
    // with the those config details.
    tcx.addOutput(
        this.address, 
        escrowAmount,  // 👈
        escrowConfig  //👈
    )
    return this.tcx;
}
```
In this transaction factory, note:

  * A UUT is created for the escrow instance  (`txnCreatingUUT`)
  * A related EscrowPolicy contract receives the UUT (`txnCreatingPolicy`)
  * A Datum (of type EscrowInstance) is included in the contract  (`escrowConfig`)
  * The EscrowInstance has a reference to the UUT and the address of the escrow policy script

## Footnote: Importing Helios source

{% callout title="Hint: Importing Helios on-chain source code" %}
    Use the rollup `string` plugin with vite, vitest, or rollup if you have trouble with the `import contract ...` line

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

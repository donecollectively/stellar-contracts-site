---
title: Stellar Community Coin
description: Open-source community coin minting
---

Open-source community coin minting

---

## Created for the Community

The Community Coin contract is an open-source project we created for the Cardano community.

## What does the Community Coin contract do?

### Makes a Good Start

The current scope of functionality in the Community Contract is designed to be a good start - a base that other projects can fork and augment.

It's also designed to be a starting point for the project itself.  How will the Community Coin project evolve?  Get involved to find out - and influence it!

### Minting native tokens based on consent

This version focuses on providing an accessible Javascript-based way for any small organization or community to **mint native tokens on-chain** by explicit mutual consent.  You can build this capability into your website or dApp directly, or specialize your implementation so it mints just the kinds of coins you need for your dApp.

Any community can have a Community Coin contract, and every such contract is completely separate (at a different contract address) from every other.

All the tokens minted by a Community Coin instance will share the same token-policy ("minting policy hash"), and can be registered on the Cardano token registry.

### Enables decentralized authority

The authority to mint tokens within your community can be held by a configurable number of trustees, each with particular credentials.  This mechanism can be the focus of further development, but supports essential decentralization as-is.

### Types of Tokens it can Mint

The Community Coin policy doesn't force any limits on the kinds of tokens it will mint. 

  * NFT's
  * Fungible or NF reputation tokens
  * Membership tokens
  * Contribution credits

The on-chain contract, as well as its off-chain Javscript class, can support creation of many kinds of transactions you design, with token-names and semantics you create.

## How It works

### Paired with a Minting Policy

We are building Stellar Contracts in a way that any leader contract can have a default paired minting policy with no code required.  Community Coin is no exception.

### Uses a Charter token

The contract has a CharterToken datum, which holds a **unique "charter" token**.  

This type of Datum currently includes its **trustee list** and a **minimum number of approvals**.

### Requires the Charter Token to be spendable

The ***minting policy will mint tokens on demand***, as long as the Charter token is included in the transaction.

The main contract script enforces trustee-approval before the Charter token can be spent, and the minting script requires the Charter token before it will mint tokens.

### Keeps the Charter Token in the contract

The main contract script also enforces that the Charter token is returned to the contract.

## A Bigger Picture

We're excited to see how people will extend and build upon the basic pattern of minting tokens.

It's part of our mission to ***make dApp development on Cardano way easier***.

### Pilot: in-contract authority configuration

Our initial authority mechanism does not rely on giving any one person all the "keys to the kingdom".  Instead, it provides an implementation supporting in-contract policies.

Future iterations of this can shift the policy details to a delegate, and allow that delegate to pick its own authorization approach.

### TODO: Proof-of-concept: contract continuity

We'd like to include additional alternative paths for authorization, to provide high assurance of continuity even if the current trustees lose their authentication keys.

### TODO: Proof-of-concept: contract evolution

Demonstrating techniques for evolving a contract would be a high-value contribution to the community.

We'd like to be able to replace the code of the contract itself.  

This would come with an address change (for the "Treasury" contract, in the case of the Community Coin), but ideally all the important elements of its charter, and even the minting-policy itself, can remain unchanged, and it can continue minting all the exact same coin-types after an upgrade.






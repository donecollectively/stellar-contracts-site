---
title: Cardano Accounting Fundamentals
description: Smart contracts for Accounting semantics
---

Proposed: Smart contracts for Accounting Fundamentals

Co-proposing: Treasury Guild?  DCypher?
---

## What are on-chain accounting fundamentals?

Qipu were some of the world's first physical devices for accounting.  The abacus was great for realtime computations, but qipu were ***viable prototypes*** of immutable semantics for ***interpersonal finance records***, easily a thousand years before the first blockchain.

The fundamentals of on-chain accounting include categories and details for all kinds of assets, money received or paid, for money that is owed (receivables) or owing (payables), and for income and expenses.  

Those fundamentals should include the standards-conformant structures of on-chain activity for setup (i.e. category-creation / chart-of-accounts maintenance), and the essential records of accounting transactions. 

With solid baseline data on the state of finance in an organization, many kinds of reports can provide insight and knowledge about that organization and its operations.

## Accounting Fundamentals for Cardano

Immutable record-keeping systems like Cardano are a great fit for accounting.  The native UTxO model is also a close fit for the way information systems (like paper books, for example) grow and evolve.

We're making **smart contracts for on-chain accounting records**.  We expect these will be operational on side-chains in future cases, but for today, we'll be focused on serving our open community's organizations with open accounting records.

### Backstory

***In 2022, the Treasury Guild*** conducted a collaborative collection of metadata schema to use for contribution-record-keeping.  They've been applying that standard mechanism to their payout process, showing consistent contributor records, including task-level tags indicating different types of contributions.  

Those on-chain records can now be queried and aggregated for a great reporting view - a staple of information-age accounting.

We're extending contribution-accounting to cover a more complete set of financial transactions to serve all kinds of organizations with great record-keeping.

### Developing ready-to-use accounting automation for Cardano.

We're working with organizations and individuals in the Cardano community with both knowledge and needs around accounting.  We're developing data schemas for all kinds of transactions beyond contributor expenses.

In this proposal, we'll develop metadata standards with community consent, with CIP documents and community workshops, for a set of ***on-chain open accounting standards***.  

We'll include open-source smart contracts with Javascript API's, that applications can use to ***directly insert accounting details from their dApps*** into their accounting system using those open standards.

## Building Out with the Community

Open standards make a bunch of things easier.  Let's do it together.

Some of our member organizations will also be developing various UI's interfacing into some of these same accounting contracts, each serving particular segments in ways unique for their applications.

### Project Sustainability

We are leaving an open choice within the Stellar Product Cooperative on approaches to ensure a treasury for the Stellar Accounting product.  That might include a subscription model, transaction-fees connected to a limited subset of transaction-types, or with value-added services plugging into the basic accounting code.

### How does it work?

Our engineering approach for accounting on Cardano is centered on the use of tokens and contract `Datum` (not transaction metadata, because on-chain contracts can't access transactions metadata).  

At the application level, we will plan to leverage the patterns and infrastructure of Stellar Contracts, in which the low-level mechanisms of transaction-building are reframed as ***APIs that application developers and end-users would recognize as being closely related to the application domain***.  Within each of those meaningful APIs would be found some of the low-level transaction-building details.

As a result, the basic Accounting APIs should be recognizably for accounting purposes.  As a sample, consider such API calls as:

```js
  txnAddingAccountCharter({
    account: "consulting, technical", 
    parentAccounts: [ "revenue", "services" ]
    authority: mustFindAccountsAuthorityUtxo("revenue"),
  });

  txnAddingToReceivables(txnAmount, {
      sales: mustFindChartEntryUtxo("consulting, technical")
      client: mustFindClientAccountUtxo(clientId)
  });

```  
  These sales-and receivable-related calls are a small sample based on our existing technical experience in accounting systems.

## How you can help

You can [join our product cooperative](/docs/contributing/join-to-earn) and support our Catalyst F10 proposal for Cardano Accounting Fundamentals.

To be a technical contributor, [join our learning program](/docs/contributing/join-to-learn) and become Stellar certified.



  


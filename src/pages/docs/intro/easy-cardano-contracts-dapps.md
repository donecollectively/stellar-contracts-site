---
title: Cardano dApps made easy
description: We'll help you break it down
---

Create the next great dApp using the best tools from Cardano. We'll help you break it down.

Break through the technical barriers, express more meaning, and **_connect UI developers more easily_** to the code that creates the right on-chain behaviors.

---

## Get Beyond Low-level Mechanisms

You're smart and insightful. Keep focusing on the meaning of your dApp, not on every low-level mechanism of Datum and Redeemer and transaction balancing.

Stellar transaction-building helpers simplify the mechanisms and assist you in making clear, well-structured **code that expresses meaning and purpose for your dApp**.

### User stories go with contract Activities

dApps built with Helios and Stellar Contracts **_put your user journeys front and center_**. People doing **Activities** are at the heart of your dApp.

```js
  @partialTxn
  txnAuthorizingContingentFunding() {  // "what it does"
     // omitted: "how it does it"
  }
```

The name of this hypothetical partial-transaction helper carries significance and intuition, even for someone who may not understand its underlying details.

### Requirements management for effectiveness and certification

The Stellar **_requirements protocol_** links those activities and their purposes together with more detailed design and technical requirements

```js
requirements() {
    return {
        "positively governs all administrative actions": {
            purpose: "to maintain clear control by a trustee group",
            details: [
                // descriptive details of the requirement (not the tech):
                "The **Creator** of the contract make an iniital a trustee group",
                "**Trustees** need to sign transactions to approve changes",
                "**Trustees** can approve changes to the group membership",
            ],
            mech: [
                // descriptive details of the chosen mechanisms for implementing the reqts:
                "uses a 'charter' token specialized for this contract",
                "the charter token has a trustee list in its Datum structure",
                "the charter Datum is updated when needed to reflect new trustees/thresholds",
            ],
            requires: [
                "has a unique, permanent charter token",
                "has a unique, permanent treasury address",
                "the trustee threshold is enforced on all administrative actions",
 ...
```

These requirements connect with designs and tests, building assurance that each component of your dApp delivers right results.

```js
describe("has a unique, permanent charter token", () => {
    describe("mkTxnMintCharterToken()", () => {
        it("creates a unique 'charter' token, with assetId determined from minting-policy-hash+'charter'", async (context: localTC) => {
            ...
            await treasury.submit(tcx, { signers: [actors.tracy, actors.tom] });
            const u = await network.findUtxos(treasury.address,
                hasCharterToken);
            expect(u.length).toBeTruthy();
        });
...
```

## Smart Patterns For Rich On-Chain Behavior

### Building blocks you can actually build with

Writing big contracts with Plutus isn't the way to go. Things get complicated too quickly.

Stellar patterns help you make smaller elements that do simpler things so **_developers can keep things manageable_** and build for the future.

### On-chain Reaction to On-chain Activities

Building reactivity on Cardano gets easier with collaborating smart contracts. No off-chain monitoring required.

Follow our architectural pattern library to learn how to simplify high-functioning dApps.

### Evolving the Immutable

You don't have to get stuck on immutability. You can use simple patterns of delegation to hook up today's version of policy logic today, and get started on mainnet.

... and tomorrow, do simple in-place updates to smoothly roll out new logic, behavior and policies.

Stellar Contracts helps you get 'er done.

# Contracts Work Group Agenda and Notes
This thread contains agenda and/or summary of the regular Contracts sync meeting. Please propose agenda items through the PRs and Issues.

## 30.07.2020

### Testing

* Voting contract testing with localnet

### Contracts

* Staking pool
  * 0.3.0 - Added view methods for list of accounts. And each account.
  * 0.4.0 - Helper methods for delegators. `deposit_and_stake`, `unstake_all` and `withdraw_all`
* Multi-sig
  * `DeleteKey` to cleanup requests created with this public key if it's on the multisig.
  
### Discussion

* Native token support. https://github.com/nearprotocol/NEPs/issues/102
* Voting for bridge upgradability.

## 23.07.2020

### Testing

* Voting contract testing is almost done.
  * Local script that verifies voting through staking pool contract
  * Testnet validators voting on behalf of the staking pools. Need to ping them. Currently is 40% done.

### Contracts

* Multi-sig is committed with multiple actions on every request.
  * It doens't have restrictions on added keys.
  * Wallet is unblocked.
* Illia fixed linkdrop callback. Issue with callback has a different argument than the method.

### Discussions
* Presentation by Alexey: Multiple contracts on the account - modules and resources.

## 16.07.2020

* Code coverage to find dead codepath in the contracts.
* Smaller/efficient serde for JSON.

### TODO
* (Evgeny) Update `OWNERS` for `near-sdk`.

### near_sdk
* Published `1.0.0` version `near-sdk` that includes runtime `1.0.0` with borsh `0.7.0` re-exported.

### Contracts
* Staking pool contract fix for insufficient stake action failure. Fixed by adding a callback to the staking action.
* Voting contract. 2 new view methods. Replaced persistent map towards to the hashmap.
* Testnet test with real validators.


## 09.07.2020
 
### Status report
* Evgeny Kuzyakov: Working on deployment scripts. Wrote the script to deploy lockup contract. Will use it for both testing of the testnet and for real deployment on production.
* Evgeny Kuzyakov: Then will work on core items in near-sdk-rs, like re-export of Borsh and serde. Not clear however whether we want to go with serde or nanoserde. The difficulty is that nearcore runtime uses Borsh and serde. In general, we should re-export Borsh from the runtime, however it is not clear whether we want to do it.
* Evgeny Kuzyakov: there are some requests for the core contracts. E.g. people want to list deligators for the pool.

### Notes
* Max Z: Let's release 1.0 version of near-sdk-rs and runtime.
* Evgeny K: There is an issue that lockup contract can commit to only one pool, which will create centralization. The current solution is to have several lockup contract per user, which will also help us to pseudo-anonymize users.
* Alexey F: Please review https://github.com/nearprotocol/NEPs/issues/90

## 26.06.2020
### Status report
* Evgeny Kuzyakov: worked on the contracts, but did not have enough time to review Matt Lockyer's contract;
* Evgeny Kuzyakov: updated the lockup contract to use external contracts, like multisig;
* Evgeny Kuzyakov: updated staking pool factory to add pause/unpause staking, it does not affect the internal math, it just unstakes the current all stake, but does not return any shares. Validators wanted it before the upgrade. If someone increases the stake the all stake is not restaked;
* Evgeny Kuzyakov: added bash script that uses near-shell to deploy core contracts. This is done using repl. This is in PR. But will need to implement multisig with lockup, which would require helper contract;
* Matt Lockyer: Pending PR that adds multiple actions to the multisig contract.

### Notes
* Max Zavershynskyi -- we need a template and the cookbook for the Solidity contracts at Near. Anton Bukov's template is pretty good, let's reuse it. We will expanding the scope of this WG to 
* Evgeny Kuzyakov -- we need to unify testing of Rust contracts. There is a lot of copy-pasting, it depends on nearcore master. We need to wrap up Rust contracts integration testing. Alexey Frolov -- we need to create a generic client that can talk with generic contract that exposes JSON API. We need tool that generates wrapper for client that will be independent from what serializer the contract is using;
* Evgeny Kuzyakov -- nanoserde might be too generic and heavy for us, we might want to write our own JSON contract.
* Max Zavershynskyi -- we have metadata is implemented: https://github.com/near/borsh/issues/79 It is not turned on, because it adds 20KiB to contracts. Evgeny Kuzyakov -- let's investigate it after `no-std/alloc` for `near-sdk-rs`.

### Action items
* Matt Lockyer: Create bounty for no-sdk borsh;
* Max Zavershynskyi: Optimize borsh schema to occupy less Wasm code;
* Evgeny Kuzyakov: Writing most space optimized JSON library (after look into nanoserde);

## 19.06.2020
### Current Focus
* Evgeny K: Worked on the lockup contract, removing access keys in order to support multisig. Multisig contract wil be able to issue actions on top of the lockup contract. This change is done, there is a PR;
* Matt L: Worked on multisig that supports multiple actions in one transaction. There are some security concerns, in case some key is compromised. Evgeny K: It might still be beneficial to add access keys on top of the multisig contract.

### Notes
* Evgeny K needs to review NFT contact that DevX team wrote, it might need to include fixes that we've done for fun token. We should also extract minting, burning into the extension.
* Max Z: We need a scheme for how we extend the contracts. Evgeny K: We might not need to enforce that the extension does not modify the original code.
* Evgeny K: In staking pool, we need to add the pause/unpause methods. No one is currently working on it.
* Max Z: We might need Evgeny K's help to add tests for rainbowbridge contracts;
* Evgeny K: There are some work items for near-sdk-rs, we need to do:
  * re-exports of Borsh and serde.
  * And we need to reduce the size of the contracts. Max Z: We need to allow both std and no-std modes for near-sdk-rs.
  * We need to make collections reusable;
  * Alexey F was working on private methods and init once.
  * We also need to make standalone available for near-sdk-rs unit tests.
  
### Next week focus
* Cleanup the lockup, multisig, staking pool contracts;
* Bridge testing;
* near-sdk-rs some improvements.


## 12.06.2020

### Current Focus
* Multisig discussions -- consensus that we don't want to have multiple transactions, but we want to have multiple actions. PR needs to be updated and we can merge it later.
* Bowen W merged the voting contract.
* We need staking pool upgrade -- to update to API for voting. Previously you could pass the stake with which you could vote, we want to restrict people to be able to only vote with all their stake.
* Evgeny K: talked to Peter about Fun/NFT tokens. We will move it to the core-contracts, they will update the API of NFT to match Fun token. Max Z: Suggestion to add security audit for each contract. Vlad F: There is a tool in Rust ecosystem that called `cargo audit` that we might want to look into.
* Evgeny K: Thinking about adding helper to host function that checks whether enough tokens were attached to cover the storage growth. It is only the problem with non-full access keys. Max Z: We need to create a criteria which we use to decide when to add host functions and when not to. Evgeny K: Let's move it to research discussion. Evgeny K and Max Z agree that it is post Phase 2 launch, at least. Alexey F: Let's include it into sdk.
* Max Z: We might need help with bridge contract testing. Evgeny K: Let's move bridge contracts to core-contracts.

### Notes
* Vlad F: It seems like we are changing the contracts that were security audited. Max Z: Suggestion to have changelog and release process for each contract and link security audits to the corresponding version;
* Vlad F: We cannot pause staking pool. We need a way to unstake the staking pool but the staking pool owner. Evgeny K: Let's add owner's method to pause/unpause staking pool.

## 05.06.2020

### Current Focus
* Addressing security audit fundings -- can be finished today;
* Bowen W is working on voting contract -- can also be finished today, Evgeny K is currently reviewing Bowen W's PR;

### Next Focus
* Gas only contract
  * Add Access key towards other contract
  * Lockup based
* Lockup. Remove access keys, use `predecessor_id`
* Lock transfers on multisig;

### Notes
* Evgeny K is writing best practices document. We need more tutorial about cross contract calls and how to test it. Runtime standalone does not provide enough debug information, too many unwraps. Runtime standalone needs to display more debug info on failures.
* We got a request for multisig to expose access keys to the contract. However, this is not trivial because we do not store the number of access keys. Max Z: iterating over access keys in contract is problematic, because it is hard to create challenges for them. Evgeny K: we can implement later unordered map for access keys. For now we can expose a lookup of a single key. For multisig we can solve it completely in the contract.
* Max Z: Our cross-contract API in Rust needs improvement. Alexey F: Researched this solution in Eth 2.0 and libra. They allow to define a resource that can be shared between the contracts. It is similar to our "safes" but they allow other contracts to modify it. The resource would live in an isolated VM and other contract might be able to interact with it.

## 29.05.2020

- Done
  - Lockup/vesting
  - Staking pool
  - Staking pool factory
  - Whitelist
  - Multisig (needs docs)
  - Fungible Token
- TBD
  - (For phase 1) Voting / Transfer poll.
  - Gas only contract
     - Add Access key towards other contract
     - Lockup based
  - Lockup. Remove access keys, use `predecessor_id`
  - Lock transfers on multisig

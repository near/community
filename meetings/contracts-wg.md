# Contracts Work Group Agenda and Notes
This thread contains agenda and/or summary of the regular Contracts sync meeting. Please propose agenda items through the PRs and Issues.

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

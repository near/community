# Contracts Work Group Agenda and Notes
This thread contains agenda and/or summary of the regular Contracts sync meeting. Please propose agenda items through the PRs and Issues.

## 05.06.2020

## Current Focus
* Addressing security audit fundings -- can be finished today;
* Bowen W is working on voting contract -- can also be finished today, Evgeny K is currently reviewing Bowen W's PR;

## Next Focus
* Gas only contract
  * Add Access key towards other contract
  * Lockup based
* Lockup. Remove access keys, use `predecessor_id`
* Lock transfers on multisig;

## Notes
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
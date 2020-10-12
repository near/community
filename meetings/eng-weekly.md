# Engineering Weekly

This meeting contains high level updates from Engineering Workgroups and discussions that must be agreed upon across.
If you have a proposal for a topic - send a PR to add it to agenda (and your email to be added to the meeting) ahead of Monday 4pm GMT (9am PST) to be included in the upcoming weekly meeting.

The live stream each Monday 4pm GMT (9am PST) can be found: https://youtu.be/nJQnSjsRAD4. 
Past meetings have their own links.

## Agenda -- Monday Oct 12, 2020

1. Updates from work groups
 - Consensus
 - Contract
 - Transaction runtime
 - Contract runtime
 - Node interfaces
 - EVM
 - Bridge
 - Wallet
 - Infrastructure
2. Research highlights
3. Discuss cutting edge running network

## Notes -- Monday Oct 12, 2020

### Consensus

Discussions around sharding design.
Speed up chunk distribution.
Stability and testing for consensus.

### Contract

Tokens for uniswap natively.
Storage staking issues, allowance around original token standard. Experimenting with new token standard.

### Transaction runtime

Upgrading fees: making progress. IO estimation, lowering fees. Should be done by end of the week.
Work on disk caching for compilation: Compilation is very expensive even in a single path mode. Caching all compiled contracts to disk.
This will bring down the base function call cost down to roughly transfer. Also correcting the cross contract call fee down.

### Contract runtime

Collecting feedback for cross contract testing to improve macroses.
Improve way to evaluate disk read/write in fee calculation. Shared with transaction runtime.
EVM fee for deploying solidity contracts. Introduced additional parameter for accounting EVM gas when counting NEAR gas.

### Node interfaces

Improving indexer infra. Change explore to using nearcore based indexer: final touches are been done, deployed on testnet and mainnet.
Identified nearcore issues that provide inconsistent info.
Rosetta RPC: maintanance work.
JSON RPC: policies around what are guarantees and expected ways to introduce new RPCs and deprecate them.
There is a near CLI written in Rust: NEP#31 and Vlad Frolov's desing around construction API. Discussion in NEP#31
Explorer: 
 - Adding lockup contracts consistent with balances in the accounts.
 - Migrate to indexer infra: more info about accounts
 - Redesign of explorer dashboard, to make basic stats more prominent

### EVM

Implementation mostly done, few open issue: gas usage, meta transaction charging.
Open question is to define how we are going to be testing this with real developers.
Defining testing plan is an open item to finalize.

### Bridge

Solidity contracts are simplified. Proofs format was changed.
Improving CLI/Library for bridge.
There is a proposal to change the structure of the bridge. Open discussion for changing this and finalize the decision before end of this week.
Migrating brdige cli to new version of nearup.
Working on product features and design to connect to NEAR EVM.

### Wallet

First version of staking: have in staging and finish up testing. Might get released Mon afternoon.
First release of lockup staking only. Everything else coming later.
Withdrawing from lockup, should be done before this Wed.
Added sending to implicit accounts (live in testnet)
Had RPC issues, that led to unclear errors on the wallet.

### Infrastructure

Terraform configuration for RPC nodes: support to any cloud provider.
Figuring out a way to spin up RPC nodes fast, by moving data.
Working on bridge cli transition.
Archival node backups for testnet and mainnet
RPC overload SEV. Canary were broken due to to testnet hardfork.
Adding "crashnet", picks latest commit and runs that.
Recovery plan: versioning failures. TODO: discuss in Infra meeting.

### Running bleeding edge

Leverage Rust compilers system.
Use compilation flags into `master`.
Protocol version can be an issue, as there will be a conflict.
Switch from number that enables feature to feature flags and just define a prtocol as a list of features.


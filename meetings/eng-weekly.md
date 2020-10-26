# Engineering Weekly

This meeting contains high level updates from Engineering Workgroups and discussions that must be agreed upon across.
If you have a proposal for a topic - send a PR to add it to agenda (and your email to be added to the meeting) ahead of Monday 4pm GMT (9am PST) to be included in the upcoming weekly meeting.

The live stream each Monday 4pm GMT (9am PST) can be found: https://youtu.be/nJQnSjsRAD4. 
Past meetings have their own links.

## Notes -- Monday Oct 26, 2020

### Chain (Bowen)

Michael is working on merging optimistic forwarging of chunk parts (it's an old PR that didn't make the cut for phase I)  
Then looks at bottlenecks for chunk distribution.

Memory leak investigations: we understand many memory leaks, and the general direction of how to fix them. Almost exclusively from external dependencies. Next step is to figure out how to address them.

Mikhail works on combining state parts. Now do it in memory, when the state is large it will OOM (not a concern short term).

Alex S rewrites syncing code: adds light client sync, GC headers.

Bowen works on the new upgrade process, with feature flags, and nightly builds.

### Tx Runtime (Eugene)

Still working on upgrading the fees. Nightly builds (discussed by Bowen above) will help testing it on release.

Compilation on deploy: first will build caching on function call, then caching on deploy. Will charge on deploy.  
Not a protocol change, thus fees are more important.

### Contract Runtime (Max)

Nikolay managed to almost complete compilation on deploy, and is disabling WASM cache (not only caching WASM bytes).

Were able to fork some of our dependencies, and now we compile nearcore with stable Rust.

Progress on cross-contract testing, in the loop with Mike, Chad, collecting feedback.  
When we compile contract, we should be able to remove debug info or exclude test logic.  
E.g. today near-sdk-rs depends on some primitives from nearcore, which on itself has many dependencies. For testing should compile with all dependencies, on deploy should only use the needed, which brings the compilation time down.

### Node interfaces (Frol)
### Explorer (Frol)

Bridging together nearcore indexer, and what explorer needs. Last stages before it's produciton ready. It is deployed on testnet, had some issues with migration.

Will have a better backend for the explorer. Current one misses execution outcomes, so lots of info for cross-contract calls is missing. E.g. staking, delegations, lockups all involve cross-contract calls.  
Initiative to fix it is Bohdan's.

Rosetta RPC still has some issues with the genesis block. We try to expose it as having 200K transactions, but Rosetta can't handle it. Looking for solutions.

JsonRPC is getting more update on the structured errors side. Trying to ship in two phases, backwards compatible. The string errors will remain, extended with the structured error.

Discussion with Matt re: our China deployment, there are issues with reaching our services from China, there's no latest update presently, need to work closely with Bo and Amos.

### Wallet (Kendall)

A release today: staking with unlocked near, partial unstaking, and new account creation flow that enables anyone to create new accounts.

After this release we will take couple cycles to fix issues, have end2end tests, work on stability for the next couple weeks.

Question from Eugene: can one create an implicit account with Ledger? Kendall's response: you'd need to have two steps.

### Infra (Sandi)

Max helped to create a new on-call schedule. For people involved: please update your calendars.

Nick is working on rolling out terraform on betanet. Then will be rolled out on testnet, then mainnet.

### Bridge (Alex Sh)

Lots of work on the architecture changes. Repos are being restructured. Some CI tests are broken because of that.

Preparing for the townhall with the roadmap:  
The first step: Deploy the bridge in the confined environment, only for the approved accounts. Will be the same as the one we used for the hackathon, but with a differnet structure, and several bugs fixed. One was a critical bug found by Marcelo. Peter brought one more person from the community who will be helping with the connector for native currencies.

### EVM (Eugene)

Mike continues working on metatx and ecrecover support. There's a version that is mostly working.

Eugene: Figuring out the initial scope.

Bo: estimating EVM tx cost, mapping between ETH gas and our gas, and how the tx size influences our gas. Making good progress, achieved less than 10% volatility in gas cost. Also wokring on tests for balancer.

Arto joined the team, experienced with EVM, will be helping with updating our EVM to latest, and help with details on EVM.

Plan to release on betanet behind the feature flag. Potentially might be forked several times, not caring about the upgradability short term.

## Notes -- Monday Oct 19, 2020

### Chain (Bowen)

Versioning ShardChunkHeader change by Michael is in.
 
 - Took a bit longer due to a failed test, that appeared flacky at first, but turned out actually revealing a bug.
 
Piotr keeps on investigating the memory leaks. All the tools are done. There are already some insights, but don't want to share prematurely.

Misha fixed the issue with repeatedly applying genesis state.

Discussion on protocol upgrades doing forward, came up with a good plan with feature flags and separation between nightly and stable.

Alex and Bowen are finalizing the approach towards enabling sharding. Have a good plan, need to polish details.
 
 - The plan is to increase the number of validators.
 
Alex also works on rewriting syncing code.

### TX runtime (Eugene Kuzyakov)

Still working on the fees. Nikolay has a PR that measures disk instructions, in addition to CPU instructions. Measuring the impact of RocksDB.

PR to reduce most of the fees this week.

Work on caching contracts. Shifting cost of compilation from running to deploying.

### Node interfaces (Frol)

Indexer and Rosetta are the current focus.  
Current explorer was misssing receipts and execution outcomes.  
Bohdan already populated all of those to Postgres, except for validator rewards. They are coming from the state changes, not part of any receipt, so harder to extract. Will work on them in the coming week.

Today do not preserve the order of execution outcomes in the nearcore database, doesn't allow properly reporting the results of the transactions.

### Explorer (Frol)

Migrating from the old backend to the new indexer. Will take some time, since the data layout is different.

Some things that confuse people and are being fixed, e.g. account balances.

Mike points out that when EVM comes, will be heavily relying on Receipts, will need support from Explorer.

### Wallet (Kendall)

- Ability to withdraw from exchanges. Have a temp verstion coming out today.
- Wednesday release targeting partial unstaking, and staking from base account (today only support staking from lockup).

### Infra (Sandi)

New release for Mainnet.
Mostly working on figuring out RPC. Terraform PR is out.

 - Allows setting up RPC for any network.
 - Figuring out how to spin up new RPC nodes quickly.
 
Mario added new monitoring, Greylock for mainnet.  
nearup improvements: log rotation  
Discourse for the community  
PTOs last week

Learned that Archival nodes die on migration on Betanet, figuring out why. Likely some big allocations.  
Bowen suggests that he didn't take into account that Betanet had lots of full blocks, and the migration doesn't take it into account.

### EVM

Figuring out the scope of all the deliverables. Working on the Roadmap.  
Likely several stages.
 1. Merge what we have now with couple small fixes, with feature flags, and ship to crash net.
 1. Several larger features, likely via bounties, ship by the end of the year. At that time ideally have sufficiently stable EVM to ship to testnet.

With the current resources no way to have stable EVM this year.

Max points out that EVM will create more work for Rosetta, namely exposing EVM accounts.

Mike asks on what networks will the EVM be enabled. It will only be the crashnet (to be renamed to Betanet as we wind down the current Betanet)

### Bridge

Alex K has an architecture proposal, that everybody appears to agree with.  
Alex K also has a proposal for test infra.

Likely can ship this year, naturally not including work items related to integration with EVM.

### Further discussion

Mike asked about NEP to burn all attached gas, Evgeny Kuzyakov responds that it has issues, and is not short term focus.

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


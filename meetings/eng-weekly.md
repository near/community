# Engineering Weekly

This meeting contains high level updates from Engineering Workgroups and discussions that must be agreed upon across.
If you have a proposal for a topic - send a PR to add it to agenda (and your email to be added to the meeting) ahead of Monday 4pm GMT (9am PST) to be included in the upcoming weekly meeting.

The live stream each Monday 4pm GMT (9am PST) can be found: https://youtu.be/nJQnSjsRAD4. 
Past meetings have their own links.

## Notes -- Monday Nov 30, 2020

### Chain (Bowen)

Continuing the same work:

* Investigating slowness in sharded network. So far narrowed down to collecting chunk parts.
  * Sometimes requests keep on being sent after the chunk is reconstructed
  * Marcelo confirmed that in the experiments there's no long routes, so the slowness can't be attributed to that
* Memory leak: adding more debug info. Mostly fixed in dependencies, now looking for issues on our end. Today leakng 6MB / day.
* Mikhail investigates why nodes ocasionally get stuck. Suspected deadlock initially, and concluded that our code is unlikely to have a deadlock, given we barely use any locks, and the ones we use can be analyzed.
* Mikhail also keeps on working on the full fix for the orphan pool issues.

Alex is asking if 6MB / day is worth fixing. We should not use more than 6GB today. Might not be worth fixing it.

### Infra / SRE (Sandi, Bowen)

Mario is still working on the terraform config for RPC nodes, as well as backups.  
Sandi is working on hardforks on betanet.

### Contract Runtime (Nikolay)

Discovered some nasty issue with contract caching, due to misinterpreting deserialization errors. Fix is available, needs some testing.  
Bo implemented a mechanism for continuous monitoring for non-compalible contracts. There could be multiple reasons why they do not compile: the obvious one is random junk instead of WASM, but also possible that something very close to correct WASM, and either intentionally or unintentionally breaking the ahead of time compilation mechanism.

Helping with memory leaks investigations.

Bowen points out that there's no currently reported memory leaks in WASM.

### Tx Runtime (Eugene) -> Runtime and Friends

A bug in param estimator that was not correcly estimating evictions.

All meetings were skipped last week (due to Thanksgiving?).

More educational videos.

### EVM (Eugene)

EVM branch was in review last week. Some minor things that need to be addressed and upgraded to master.

Adding features to actually use EVM, new test to cover precompiles in EVM.

### Bridge (Alex Sh)

Issue with the beta bridge: at some point default was changed from payable to non-payable, and the contract on the betanet was not able to receive tokens. Flux hit the issue. With the issue fixed, Flux is live on mainnet. (Max points out that was an SDK change, not protocol change).

Wrapper NEAR contract is on PR. Now work on NFT connector.

Work on Front End.

### Node interfaces (Frol)

Received a PR for borsh that adds no-std support.

Another PR for adding a time-machine (a bounty).

### Explorer (Frol)

Close to migrating to the indexer infra. The backend is close to supporting both backends at the same time.

### Wallet (Patrick)

Released lots of bug fixes and refactoring, especially around enabling and reconnecting ledger.

Fixed issues with staking, where the delegator was inactive or kicked out, it was not shown in the list.

There's an issue with displaying staking rewards if there's a 2FA on a non-lockup account.

Ability to skip 2FA on account creating; Reducing an amount needed to require 2FA to Ⓝ200.


## Notes -- Monday Nov 16, 2020

### Chain (Bowen)

Debugged the problem with validators being kicked out in a 8-shard system even without load. Turned out was a configuration issue.  
With a moderate load (50tps per shard) the same problem emerged, due to not enough chunks.

Memory leak -- great progress. The Wasmer memory leak is fixed. Some leaks in the cache are gone due to getting rid of the cache.  
Some memory leaks were misreporting due to not handling `munmap` properly in the memory leak detector.  
The issue on RocksDB is also figured out -- we were allocaging 8.5GB cache. Reduced to 2GB cache.

Misha continues investigating the issue with `is_height_processed`. Have a repro of certain attacks, fixing it.

Alex keeps working on building light speed sync / GCing of headers.

### Infra / SRE (Sandi, Bowen)

Focus on Betanet hardfork. Transition from the stakewars network (discontinued) to a new network for nightly releases. Will redeploy every 24 hours.

Investingating issues with RPC timeouts. Added more metrics to collect more data.

### Contract Runtime (Nikolay)

PR reviews that adds cache to the view-only contracts.

Memory growth cost: William was investigating a possibility of applying cost per additional allocated page. Initially used the same approach as param estimator, which was not appropriate due to mostly-kernel nature of pages allocations.

More work on documentation.

### Tx Runtime (Eugene) -> Runtime and Friends

Blocked on param estimator, found a bug in param estimator which was incorrectly estimating the cost of storage. There's a PR to fix it.

New request: DELETE STATE, that deletes the entire state of a contract.

New request: add trie iterator. Will not implement it. People should use `TreeMap` for iterable sorted collections.

People get surprised when move from Testnet to Mainnet that accounts cannot be just created. Potentially need to make testnet match mainnet behavior.

A new video that covers fungible token standard.

### EVM (Eugene)

Upgraded EVM to the 2.7.2. Making sure the existing tools still work and pass tests.

Almost ready to merge EVM into master under a nightly feature flag.

More tests + work on metatransactions.

### Bridge (Alex Sh)

Private beta runs on mainnet.

Open for partners, assembling the set of developers who will be testing the bridge.

Ran test transactions between the two mainnets, successfully.

A major discussion on upgradability of the bridge.

Next: private beta, further work on upgradability.

### Node interfaces (Frol)

https://github.com/near/nearcore/discussions/3597

The state of indexer grows very fast, need to be aware of that.

Considering using OpenRPC spec in Q1 2021. Might also choose plain HTTP API instead of JsonRPC. The two (plain HTML and OpenRPC) are in conflict, still figuring out.

We might want to downgrade our RPC nodes to make our resources consumption reasonable.

### Explorer (Frol)

https://github.com/near/near-explorer/discussions/470

### Wallet (Kendall)

Last week pushed UI changes to staking, to make it more clear when you have a lockup.

More recovery methods when your primary recovery is ledger.

### Further discussion

Max encourages people to remember that the work of all teams must connect to the teams OKRs.

Stephano re-emphasizes that the betanet was reset. Now people who wish to test running a node should either join the testnet or open shard alliance.  
Frol is asking if the new betanet is from scratch, or is a continuation of the old one. It's a brand new network.

## Notes -- Monday Nov 9, 2020

### Chain (Bowen)

Investingating perf bottleneck for sharding.  
There was a bug in the benchmark. Currently with 100 validators they get kicked out, likely due to epoch length too short.

Memory investigation: found two places in WASMER where it is leaking memory.
One related to a cache we are using, which is already removed in master.

Misha is looking into failures of stress.py in nightly.

Preparing to run betanet with nightly features feature.

### Tx Runtime (Eugene)

Still working on the fees. Blocked on param estimator. It started failing for an unknown (yet) reason, Bo will look into it.

### Contract Runtime (Nikolay)

Investigating the memory leak. As Bowen said above, likely one memory leak is removed as we disabled the cache.  
The other one is being investigated.

Another unresolved issue is view-only contracts. For them the execution is way more expensive if the cache doesn't have the contract for some reason.  
To solve it we need to allow them write access to the cache.

### Node interfaces (Frol)
### Explorer (Frol)

The progress last week mostly on the indexer side. Ran explorer indexer without any issues syncing from both testnet and mainnet and populating the postgres. Need some column naming, more indexes.

No progress on Rosetta last week, wants to spend more time in the coming week.

Discussion on Explorer with the community: https://github.com/near/near-explorer/discussions/467

### Wallet (Kendall)

https://github.com/near/near-wallet/pull/1133

Fixing several bugs.

Pushed three features into this release to allow for the testing: disabling 2FA, available balance update, adding / removing ledger if Ledger is the only recovery option.

### Infra / SRE (Sandi, Bowen)

Testing the terraform config. Need it for RPC nodes.  
On the release side preparing the change for the betanet release: both the new genesis, and the changes in the pipeline.  
Mario is investigating some issues with RPC nodes. Brief benchmark doesn't show any obvious problems.  
Setting up canaries for testnet and mainnet.

### Bridge (Alex Sh)

Working towards private beta release. After the major refactoring that we discussed last week, the bridge is restored to the normal conditions. It is now is compiling again. There are some tests that are still not passing.

Working with Nayduck integration. The major issue before the beta release is some work on the config, that Frol is working on it.

Next call topics: upgradability, and some security considerations.

Chad is working on the frontend. Will be published for public around December. Also props to Corwin for help with the interface.

Private betanet applications start this week.

### EVM (Eugene)

Evgeny needs to do an internal upgradability. Preparing to merge the EVM PR into master, need to fix some test failures and hide it behind the nightly flag, and that the merge doesn't pollute any part of the protocol when the nigtly feature is off.

Good progress on the rest of the features, metatransactions work now. Merged in the changes from OpenEthereum, checking if it's compatible with what we have been using.

## Notes -- Monday Nov 2, 2020

### Chain (Bowen)

Michael was measuring TPS with sharding, with 50 validators the TPS scales almost linearly with the number of shards. At 8 shards we achieve around 5000 TPS.
With 100 block producers there are still unknown bottlenecks.

Still investigating memory leaks from Wasmer, and from RocksDB. For the former Piotr tries to reproduce locally, and to submit to Wasmer.  
On the RocksDB side needs more investigation how some caches are used to understand why it is leaking.

Alex is still working on the change to enable garbage collecting headers and light speed sync.

Bowen works with others on new upgradability technique that enables different sets of features enabled on different networks.

### Tx Runtime (Eugene)

PR is mostly done to upgrade the fees.
Waiting for final asjustments re: IO costs. Will run an estimator, and adjust the fees to account for IO.

### Contract Runtime (Nikolay)

Merged contract caching. Not fully done. As mentioned above, will need to update costs, to account for the new model of contract deployment, in which we never recompile contracts.  
Nikolay is inclined to start with separating, and first upgrading the costs to account for the new deployment model, and then separately to account for IO.  
Managed to remove the requirement to use nightly Rust. Only Wasmer required it, and it appeared that the dependency in Wasmer was small, and we just removed it.

### Node interfaces (Frol)

Lots of focus on Indexes, work by Frol, Bohdan and Yifang

### Explorer (Frol)

https://github.com/near/near-explorer/discussions/463

### Wallet (Kendall)

Last week released on Tuesday three major features:

- Staking UI for non-lockup tokens;
- New account creation UI with implicit account intermediate step;

For the next couple releases will be concentrated on some UI bug fixes.
Post bugs here:
https://github.com/near/near-wallet/projects/5

Adding an ability to disable 2FA (which will also release 35NEAR that are held on the account to pay for 2FA storage).

### Infra (Sandi, Bowen)

At this stage defining milestones.

Mario will be working on investigating RPC perf.

Nick is working on Terraform config for RPC nodes, and figuring out a better way to manage our nodes, especially for migrations (speaking of Testnet, not Mainnet).

Sandi and Bowen will be driving the transition for nearcore to switch to using feature flags for Betanet, will require a hard fork of Betanet.

Finalized oncall schedules, realized we need a better handbook.

### Bridge (Alex Sh)

The bridge team was focused on refactoring repos, most of the work besides CI is completed.  
Props to Marcelo for finding a critical issue in the token factory, and fixing it.  
Props to Chad for the work on the UI.

Current focus: releasing the beta on the mainnet.  
And upgradability for smart contracts.

### EVM (Eugene)

Making good progress. Mike finishes examples, and updates tools (near-cli, near-api). Want to introduce upgradability before we merge those changes.

Plan to push to master next week behind the nightly compile-time flag.

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


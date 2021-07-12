# Protocol Team Agenda and Notes

This thread contains agenda and/or summary of the regular chain sync meeting. Please propose agenda items through the PRs and Issues.

Things that the chain team work on include:
* Consensus
* Sharding
* Network
* Cryptographic primitives

## Agenda

### Transaction runtime

### Chain
- https://gov.near.org/t/challenges-of-state-challenges/2028

### Network

## July 12th, 2021

### Chain + Transaction runtime

Egor:
- Done: runs apply_chunk in one thread per shard
- Todo: Run a real node with the change to make sure it works well; write multi-shard tests for performance and stability; run nayduck tests to do a sanity check

Alex Logunov:
- Done: restore receipts in database through a database migration; work with runtime parameter estimator to update gas costs (reduce function call base cost) and observe a lot of discrepancies with the current costs
- Todo: explain the discrepancies in contract deployment cost and data receipt creation cost (50Tgas)

Min:
- Done: Writing a proposal to split current state into multiple shards. Two plans: off-chain approach and on-chain approach. Main difference is whether we have new state roots committed in block headers during the epoch before the split is done.
- Todo: Decide which plan to proceed with; discuss it with the team this Thusday

Evgeny:
- Done: working on tests for host functions. Will have some tests ready this week
- Todo: tests for other host functions not yet included

Discussion:
- Reconsider hardware requirements and maybe use better SSDs and maybe consider separating state storage.

## July 6th, 2021

### Network

Piotr:
- Done: submitted the PR that changes the routing table exchange to nearcore; will continue testing the PR
- Todo: Merge the NEPs PR; look into python tests for the change; look into spam protection

## June 28th, 2021

### Chain + Transaction runtime

Egor:
- Done: finish PR to document the triggers when a chunk is applied. Look more into how to parallelize runtime
- Todo: Keep working on parallel runtime

Alex Logunov:
- Done: finished work on contract compilation after state sync. Reviewed some PRs
- Todo: migration to restore receipts in the database

Evgeny:
- Done: working with Joshua on math api pull request
- Todo: tests on the math api implementation

Min:
- Done: finished a python test (infinite loop) and fixed the issue with refund
- Todo: Look into splitting the state into eight

Bowen:
- Done: changed the constant for maximum gas price

### Network

Piotr:
- Done: submitted NEP PR for routing table exchange and addressed comments from Bowen and Marcelo
- Todo: update the pull request for nearcore; add rpc to get network info for testing purposes

## June 14th, 2021

### Chain + Transaction runtime

Egor:
- Done: look more into parallel runtimes and need to write more tests, but didn't find anything unexpected so far
- Todo: PR to add documentation on runtime triggers https://github.com/near/NEPs/issues/191

Evgeny:
- Done: working on the math API PR for Aurora and found some issues. Major issue: blake2 interface is not correctly implemented
- Todo: work with Joshua to resolve the implementation issues and add tests

Min:
- Done: read protocol spec, small onboarding tasks, reading the rust book
- Todo: finish watching the youtube videos on runtime & chain; looking into the onboarding task (pytest)

Alex Logunov:
- Done: Submitted PR for contracts precompilation, simplify process_blocks tests a bit
- Todo: address suggestions in PR; find why another PR led to betanet crash; add brief docs about Trie; add database change needed for indexer to restore lost receipts

Discussion
- https://gov.near.org/t/gas-price-auction/1423/17: validators do not have a very strong incentive to not include some transaction due to how rewards work
- local receipts: intrashard receipt executed in the same block
  * hard to change it back
  * local receipt is different because an account is always on the same shard as itself
  * do we do it only once or would we execute all generated receipts in the same way
  * Evgeny: have support for whether some transaction is included on chain
- Host errors
  * When the contract execution fails, there is a large enum of errors. Evgeny: too much complexity
  * Max: it serves as a way to document the errors because we need to keep track of all of them (deterministic vs. non-deterministic)
  * Max: the error is returned from a number of places (storage, runtime, vm)
  * The exact error variant does not affect the protocol, so we are free to change the error variants as we wish.



## June 7th, 2021

### Chain + Transaction runtime

Egor:
- Done: understand actix framework (for parallel runtime); help contract runtime team investigate gas cost inconsistencies
- Todo: documentation on runtime triggers

Alex:
- Done: implemented compilation of contracts when state changes. Debug tests on state sync that should trigger compilation of contracts. Fix protocol version in the maximum gas price change and found that some tests no longer work after the recent refactoring of nearcore.
- Todo: fix the test on state sync; add database change needed for indexer to restore lost receipts

Evgeny:
- Todo: review host function implementations needed for Aurora

Discussion:
- https://github.com/near/nearcore/security/advisories/GHSA-r3c6-jwgp-p825
- Disable nayduck tests that constantly fail

## June 1st, 2021

### Transaction runtime

Egor:
- Done: investigate how to parallelize runtime. We have most of stuff done already (applying chunks in parallel and then merge the results)
- Todo: start implementing parallel runtimes; spec for order of triggers when we apply a chunk

Alex Logunov:
- Done: Merged PR for gas burnt vs. gas used. Fixed a bug related to restoring lost receipts that led to the betanet crash. Continue working on compiling on contracts at deployment; look into the fix needed for indexer for restoring receipts
- Todo: Check with Alex and Nikolay on the best way to refactor contract compilation in vm runner. Look further into saving lost receipts to the database

Discussion:
- Evgeny: we may need to recompile all contracts if some config values change.

### Chain

Discussion: https://gov.near.org/t/store-protocol-config-in-a-contract/2105/4

## May 24th, 2021

### Transaction runtime

Alex Logunov:
- Done: merged PR to fix lost receipts. Submit PR to cap maximum gas price to 10 * minimum gas price. Update PR to document burnt gas vs. used gas
- Todo: compile contracts after state sync; research the idea of global storage

Egor:
- Done: Working on specs of how apply works, research for best way to parallelizing runtime
- Todo: Get ready specs PR, continue research into parallelizing

Discussion
- Evgeny: we should compile contracts when we update the state (as a function of the state).

### Chain Sync

Discussion
- Tests fail on the PR that adds time travel to nearcore.
- Use balanced bst for state.
- Have nested tree (tree for accounts and inside an account have another tree for its key-value storage)

## May 17th, 2021

### Transaction runtime

Egor: 
- Done: Merged PR to fix storage usage and find that there is some gas usage inconsistency. Started on some documentation work on the runtime spec
- Todo: Help contract runtime team to investigate gas usage inconsistencies. Investigate what needs to be done for parallel runtime

Alex:
- Done: Addressed comments on the PR that fixes the receipts that were stuck. United the approaches to apply "migrations"
- Todo: fix the tool that dumps receipts that need to be processed.

Discussion
- the order of local receipts vs. delayed receipts
- Run wasm in wasm

### Chain

Bowen:
- Done: new release that stabilizes multiple protocol features.

Discussion:
- Resharding of the current state: `aurora` will go into its own shard and everything else will go into another shard.

## May 10th, 2021

### Transaction runtime

Egor:
- Done: PR for storage fix is basically done. Need approvals from Frol and Nikolay; thought about parallel runtime
- Todo: work on parallel runtimes

Alex Logunov:
- Done: Fixed previous implementation of restoring receipts, solved cases when an epoch starts with empty chunks, implemented tests.
- Todo: Address comments on the PR and finish it.

Discussion: split of transaction and receipt gas limit

### Chain

Discussion: Use on-disk hashtable based storage instead of rocksdb to achieve better storage read and write performance.

## May 3rd, 2021

### Transaction runtime

Egor:
- Done: finish up the storage cost fix. Decide to fix the storage usage of all affected accounts in one block instead of doing the update lazily so that accounts that are not touched for a long time can also have correct storage usage.
- Todo: finish the change to storage cost fix

Alex Logunov:
- Done: implemented fix for receipts that got stuck. Working on tests for the fix.
- Todo: finish the tests for the PR that fix receipts.

### Chain

Bowen:
- Done: prepared for the new release that stabilizes multiple new protocol features. Also fixed some python tests

### Network

Piotr:
- Done: working on finishing the NEPs PR on routing table exchange
- Todo: finish and submit the NEPs PR

## April 26th, 2021

### Transaction runtime

Egor:
- Done: almost done with storage usage fix PR (only need to add description for the csv file)
- Todo: parallel runtime

Alex:
- Done: working on receipts fix to fix transactions that were stuck. Had some trouble using state-viewer due to database version mismatch between master and release branch
- Todo: Keep working on the receipts fix

### Chain

Bowen:
- Done: merged the apply chunk fix to master

### Network

Piotr:
- Done: fix some bugs in the routing table exchange implementation; adding more test cases
- Todo: submit NEP PR on routing table exchange

## April 19th, 2021

### Transaction Runtime

Egor:
- Done: noticed some bug with versioned account due to change in serialization and changed account struct to have an enum as a field; storage usage fix PR is mostly done
- Todo: finish PR on storage usage fix

Alex Logunov:
- Done: fix upgradable test on account deletion change PR and found some issues with python https package on different platforms; Investigated global storage https://github.com/near/NEPs/issues/93

### Chain

Misha:
- Done: small refactor of `ChainStoreUpdate`; opened an issue for a bug for `cross_shard_tx`
- Todo: look into performance of neard

KPR:
- Done: updated `send_approval` to not send approvals during epoch sync; `check_send_announce_account` fails; also found that some data are not properly garbage collected
- investigate more the epoch sync test

### Network

Piotr:
- Done: testing and fixing issues with the routing table exchange; need to add more tests to cover corner cases
- Todo: submit NEP PR on routing table exchange

## April 12th, 2021

### Transaction Runtime

Egor:
- working on putting calculated storage usage data into the right place
- parallel chunk execution

Alex:
- encountered balance check error on delete action cost while changing the refund to transfer and figured out the issue; PR is ready
- storage for contracts; potentially work on spec for near-vm-logic

Discussion:
- https://github.com/near/NEPs/issues/93

### Chain

Misha:
- Done: give up on rocksdb compaction issue. Hypothesis is that it is caused by oom (which triggers some bug in rocksdb). Found a bug in chain related to the first block of the epoch. It may be rejected if the node is temporarily on the wrong fork.
- Investigate the other possible such bugs caused by `ChainStoreUpdate` caches

Bowen:
- Done: fix migration of archival nodes for epoch validator information introduced in the fix for validator rpc.

Discussion
- https://gov.near.org/t/an-overview-of-possible-sharding-roadmaps-with-some-proposals/1115

### Network

Piotr:
- Done: working on testing the routing table exchange; currently the test is manual
- Todo: need to automate the tests; potentially add the ability to print network topology; submit NEPs PR

Discussion:
- Use of `RwLock` may cause slowdowns
- needs a good way to measure whether some change regresses the performance of neard

## April 5th, 2021

### Transaction Runtime

Egor:
- Done: dump of current state of mainnet and calculated the storage usage difference (~3000 accounts)
- Todo: fix storage account usage using the dump of state from mainnet

Alex Logunov:
- Done: implicit account creation on account deletion is mostly done (balance checker issue is resolved)
- Todo: fix failing tests and add correct fees to VMLogic

Discussions:
- gas prediction issue: transactions are paid in NEAR and receipts pay for its storage

### Chain

Misha:
- Todo: try investigating more the rocksdb issue. If still cannot find anything, will move on

Bowen:
- Done: fixed some python tests

Discussions:
- possible issue with new validator selection algorithm: may lead to possible attacks
- ideas: using fixed threshold (or a fraction of total supply)

### Network

Piotr:
- Done: Finish debugging tools (connect to a node and get data from the node); working on NEP PR
- Todo: finish NEP PR; testing the prototype of routing table exchange

## March 29th, 2021

### Transaction Runtime

Egor:
- Done: fixed rust expensive tests not running on nayduck with nightly features; PR to recalculate storage usage, but realize that this is too slow to run since it requires iterating over the account state.
- Todo: Switch to another approach to fixing the storage usage of accounts; look at how state storage is implemented

Alex:
- Done: merged PR to set transaction size limit; working on implicit account creation through account deletion
- Todo: finish the work on account deletion

Bowen:
- Done: Fix rust expensive tests that failed because of the access key nonce range change

Discussion:
- Account upgradability: store both new state and old state and migrate old state to new state

### Chain

Michael:
- Done: working on validator selection algorithm
- Todo: finish validator selection algorithm

KPR:
- Done: Testing epoch sync and found that some tests pass by mistake
- Todo: change syncing state machine and `reset_heads_post_state_sync`

Bowen:
- Done: restrict rpc view state to accounts with small state

### Network

Piotr:
- Done: Testing the new implementation for routing table exchange and implement rpc to control which peers a node connects to; set up terraform config for deployment of nodes for testing purposes; deduplicate edge verification; fix unnecessary memory allocation in routing table update caused by suboptimal implementation of `Hash` for `PeerId`.
- Todo: NEP for routing table exchange; finish routing table exchange testing

Bowen:
- Todo: merge the (incomplete) network spec PR

Discussion:
- idea: building a python client that only knows how to establish connection and do routing table exchange

## March 22nd, 2021

### Transaction runtime

Egor:
- Done: fixed python tests due to account version changes
- Todo: fix some rust expensive tests that fail because of account version change; submit PR to fix storage usage; study how our MPT works

Alex Logunov:
- Done: PR to set limits on transaction size; read on borsh
- Todo: finalize the PR on transaction size

Bowen:
- Done: discussion on async execution as well as storage cost

Discussion:
* storage cost

### Chain

Michael:
- Done: PR to add versioning to `ValidatorStake`
- Todo: work on the new validator selection algorithm

Misha:
- Done: fixed tests failure. Found crash related to rocksdb (could be refcounted values)
- Todo: keep investigating rocksdb issue

KPR:
- Done: work on epoch sync and fixed epoch_sync_and_then_epoch_sync.py
- Figure out how to clear data properly before epoch sync.

### Network

Piotr:
- Done: wrote the prototype on routing table exchange and start testing the change; found the cause of occasional slowness of routing table computation (iterate through all edges)
- Todo: Test more the routing table exchange; Write a NEP on routing table exchange

## March 15th, 2021

### Transaction runtime

Egor:
- Done: reintroduced account versioning PR with migration script and additional tests; work towards second account version
- Todo: Finish the change to account version v2

Alex:
- Done: submitted PR for transaction size limit
- Todo: start looking at reusable contracts

Bowen:
- Done: Finished access key nonce change

Discussion: shard receipts congestion & global key-value storage

### Chain

Michael:
- Done: Implementation for block and chunk validator selection: Add versioning to `ValidatorStake`
- Todo: Continue working on the implementation for the new validator selection algorithm

Misha:
- Done: fixed a number of python test failures (jsonrpc change broke a number of tests)
- Todo: Look at more test failures & running clusters with nayduck

KPR:
- Done: New python tests for epoch sync and they do not work on top of current master; most of epoch sync is implemented
- Todo: work on fixing the python tests.

Bowen:
- Done: Made a patch release for testnet to fix some performance issues and rpc issues.
- Todo: Stabilize some nightly features for the next major release

Discussion: cross_shard_tx test failure due to lack of forkfulness

### Network

Piotr:
- Done: prototype for routing table exchange; PR to verify each edge only once (reduce time spent on edge verification by 10x); cleanup to update routing table at most once a second
- Todo: test the routing table exchange prototype

## March 8th, 2021

### Transaction runtime

Egor:
- Done: Merged versioned account change but then got reverted
- Todo: Fix versioned account change; introduce account version V2

Alex Logunov:
- Done: deployed a simple contract & send transactions to call some methods; trying python tests
- Todo: set transaction size limit

Bowen:
- Done: made some progress with access key nonce implementation
- Todo: Finish the access key nonce change this week

Discussion: 
- Backward compatibility of state record deserialization

### Chain

Michael:
- Done: finished the modeling on block and chunk producer selection algorithm; submitted PR on the new algorithm to NEP 
- Todo: look into pytest failure caused by the chunk forwarding feature

Misha:
- Done: finished prototype for trie iterator using batch read but didn't get much performance gains; fixed some pytest failures
- Todo: look more into other pytest failures

Bowen:
- Done: merged the PR that fixes the validator rpc endpoint
- Todo: look at some test failures/flakiness

KPR:
- Done: Epoch Sync / GC headers skeleton
- Todo: Epoch Sync / GC headers enabling and testing

### Network

Piotr:
- Done: prototype for updating routing table
- Todo: Submit PR to document the routing table exchange algorithm; investigate potentially slowdown in routing table recomputation


## March 1st, 2021

### Transaction runtime

Egor:
- Done: Account versioning and encounter some issue with serialization since we need to deserialize it differently
- Todo: Finish account versioning change and then start looking into parallel runtimes

Alex Logunov:
- Done: finish reading nightshade paper & watch youtube walkthrough videos; tried to send some transactions on testnet & mainnet
- Todo: try to understand transaction lifetime by reading code; deploy contracts and send transactions; look into transaction size limits

Bowen:
- Done: organize meeting to discuss issue with access key nonces and posted a [thread](https://gov.near.org/t/issue-with-access-key-nonce/749) on gov.near.org
- Todo: Finish this discussion and move forward with implementation

Eugene:
- Done: [PR to add a new host function](https://github.com/near/nearcore/pull/3971). Adds `alt_bn128_g1_multiexp` and `alt_bn128_pairing_check` to the set of host functions.
- Todo: bundle the new cost configs

Discussion:
- Account versioning upgrade: check the size of account since it is currently static
- Rethink nightly features and how they are used

### Chain

Michael:
- Done: update model for the validator selection algorithm to include delegation. Write a simulation for the algorithm
- Todo: add more parameters to the simulation and post results

Misha:
- Done: pushed the PR to combine state parts on disk & reset_data_pre_state_sync improvements
- Todo: prototype trie iterator speed up: batch rocksdb requests

KPR:
- Done: finalized decision on how to introduce epoch sync (doing header gc).
- Todo: Submit PR on skeleton of epoch sync code to avoid further merge conflicts; open an issue on gc_after_sync.py

Bowen:
- Done: addressed comments on the PR to fix validator rpc; submit a PR to temporarily address issue with state requests
- Todo: look into some python test failures

Alex:
- Done: fix cross_shard_tx tests and found that block sync doesn't work if head is not on canonical chain.
- Todo: look into remaining stress.py failures

### Network

Piotr:
- Done: document on routing table exchange (use random value for ibfs for each connection); submit PR to not verify edges twice; changed FramedWriter to drop messages when there are too many messages and can measure the size of the buffer and other stats for debugging; add metrics to prometheus
- Todo: finish the PR on routing table exchange

## Feb 22th, 2021

### Transaction runtime

Egor: 
- Done: PR reviews & reading
- Todo: version account change

Alex Logunov:
- Done: Read first half of the nightshade paper
- Todo: look into code related to transaction handling and dig deeper into the codebase

Bowen:
- Done: Submitted the NEP PR to add secp validator key
- Todo: finish up the PR and address review comments

Discussion:
- We should potentially expose prev_block_height, block_hash, and storage usage to contracts.

### Chain

Michael:
- Done: investigate proposed block and chunk producer algorithm
- Todo: do some simulation to understand better the ratio of stake between block and chunk producer

Misha:
- Done: PR to fix combining state parts; fix memory usage in state sync
- Todo: speed up trie iterator and do some benchmarks

Alex:
- Done: debugging cross_shard_tx tests failure. It doesn't appear to be caused by tx_validity_period
- Todo: keep debugging this test

Bowen:
- Done: finish up the PR on addressing validator rpc slowness
- Todo: address some PR review comments

Discussion:
- block and chunk producer selection algorithm https://gov.near.org/t/block-and-chunk-producer-selection-algorithm-in-simple-nightshade/66

### Network

Piotr:
- Done: add filtering by thread id to the memory debugging tool; benchmark for memory_stats and it can be improved; prototype for the algorithm to exchange routing table exchange; update the document on the algorithm; thinking about potentially replacing actix
- Todo: read code & choose the right algorithm to implement for routing table exchange; post ideas regarding actix

## Feb 16th, 2021

Michael:
- Done: profile time spent per transaction/receipt for single shard network and found that it takes longer to execute over time. Could be related to database performance; start looking into validator selection algorithm
- Todo: Keep working on validator selection algorithm

Piotr:
- Done: reading research paper on how to efficiently exchange routing table information; start writing a document on the new approach
- Todo: Start thinking about potentially replacing actix; finish the document on the new approach on exchanging routing table exchange

Misha:
- Done: fixed a bug in trie iterator (not a protocol change)
- Todo: working on combining state parts on disk (almost done)

Egor:
- Done: Working on fix for correct storage usage and adding versioned account
- Todo: start working on parallelizing runtimes

kpr: 
- Done: Epoch sync passes CI test. Working on nightly tests; thinking about passive header gc (garbage collect headers during epoch sync only when a node starts)
- Todo: check Bowen's suggestion on avoiding sending block infos during epoch sync, which will eliminate the need to migrate block infos in database; fix nightly tests and add additional python tests for epoch sync

Alex Logunov:
- Done: started onboarding; learning rust
- Todo: read nightshade and possibly doomslug paper

Bowen:
- Done: finish up the PR to save transaction hashes in state; looked into RPC timeout issues and submit a PR to fix the validator rpc endpoint; merged the PR to provide a new endpoint to query protocol config
- Todo: finish the PR for validator rpc endpoint and look more into rpc issues

Alex:
- Done: working on stress.py
- Todo: looking into cross_shard_tx failures

## Feb 9th, 2021

Michael
- Done: benchmark for single shard tps, found that the single shard test fails after a long time under 800tps. `apply_transactions` takes longer over time.
- Todo: Figure out why `apply_transactions` takes longer over time.

Piotr:
- Done: Optimize routing table computation by 200x (using hashmap was slow because of hash computation). Update statisticis printed per thread to include message type. Update near-memory-tracker to be able to publish to crates.io. Research routing information exchange algorithms
- Todo: Prototype the algorithm for routing table exchange

Misha:
- Done: combining state parts on disk; fix the flakiness in a test
- Todo: keep working on combining state parts on disk (will take another week)

Egor:
- Done: fix storage usage
- Todo: finish fixing storage usage and look into parallel runtime

kpr:
- Done: epoch sync now passes sanity tests (gc_after_sync.py)
- Todo: fix tests due to the introduction of epoch sync (tests related to storage validator all fail now).

Bowen:
- Done: helped with change to reduce storage cost by 10x; fixed a bug we introduced when fixing the receipts id; released a version for testnet
- Todo: finish up the change to rpc to add protocol config; help with investigation of sharded network test failure

Alex:
- Done: change state sync timeout to be configurable and fixed some nightly tests; investigate stress.py failure
- Todo: investigate stress.py wipe data failure (a node cannot sync); nayduck distributed test

## Feb 2nd, 2021

Michael
- Done: upgrading actix and tokio to the latest version; Stop validation rotation in the test to make sure that they only track one shard at any given moment; 8 shard network can now sustain 4k tps. 
- Todo: measure 1 shard tps again to see how well we scale now

Piotr:
- Done: adding more stats to measure performance and found that there was a bug in tokio 0.2, which is used by actix 0.9. Now the performance stats match the actual cpu usage accurately. Merged memory stats to master, which prints memory usage of every thread every minute.
- Todo: Working on the new algorithm for exchanging routing table information; looking into routing table calculation and optimizing it

Misha
- Done: merged epoch manager spec; merged orphan verification PR; reduced contetion on trie cache lock by bypassing the cache when doing state parts calculation
- Todo: combine state parts without using too much memory (write intermediate results into database); verify orphans once

Egor
- Done: PR to fix receipt ids
- Todo: calculation of storage (due to a previous bug we had)

kpr:
- Done: PR that updates block header with epoch info hashes
- Todo: keep working on epoch sync

Bowen:
- Done: help Michael investigate the bottleneck with 8-shard network; help frol upgrade dependencies to the latest version to address the high cpu usage; prepare and organize a meeting to discuss secp key change with validators
- Todo: land the change that lowers storage cost by 10x and cut the new release for testnet to make sure that partners have what they need for EthDenver.

## Jan 26th, 2021

Michael:
- Done: working on sharded network performance. Investigate various bottlenecks. Tried ignoring signatures and avoiding duplicate messages; they help somewhat but still do not make the test with 4k tps work.
- Todo: continue investigating the sharded network performance

Piotr:
- Done: add new repository (near-memory-tracker) to near. Tools to understand how much time we spend inside actix. Come up with an algorithm to exchange routing table information much more efficiently. There is a tradeoff between number of roundtrips and the amount of data required to be exchanged
- Todo: investigate how to measure the time spent inside `poll`; investigate more into cpu usage of a node (might be some issue with actix); write down a more detailed version of the algorithm to exchange routing table information

Misha:
- Done: finished spec for epoch manager, PR submitted
- Todo: finish PR with orphans; work on fix trie cache bottleneck

kpr:
- Done: change the design of epoch sync slightly to reuse existing code. Investigate transition from epoch sync to header sync
- Todo: figure out which solutions to go with for epoch sync to header sync

Egor:
- Done: working on PR with receipt ids. Need to write an additional test to verify receipt ids work with forks
- Todo: understand better the protocol to write the test

Discussion:
- Add secp key change: do the upgrade of `Stake` action and contracts from runtime directly without adding new actions
- Piotr presented briefly the new algorithm for exchanging routing tables.

## Jan 19, 2021

Michael:
- Done: seals PR almost finished; Some issue with the actix buffer on the receiving end of the network. Speed of reading from this buffer is at most 2kb/s.
- Todo: investigate this slowness in reading from buffer

Piotr:
- Done: finish moving memory tracker to separate repo. Reading actix code to understand it better.
- Todo: finish stats on upload/download size from network

Misha:
- Done: working on epoch manager spec
- Todo: finish epoch manager spec

Kpr:
- Done: block ordinal PR merged; finish epoch sync implementation
- Todo: testing epoch sync. Figure out how epoch sync to header sync transition works. Separate epoch sync and header gc.

Egor:
- Done: submit PR for receipt id fix
- Todo: finish onboarding

## Jan 12, 2021

Michael: working on slow chunk distribution in sharded network. Looking into whether something is slow on the receiving side. 2k tps works fine with 8 shards. Also working on seals.

Piotr: cleaning up memory investigation code and move some code to a different repositories. Will start working on network stats (size of buffers on both sending and receiving end)

Misha: Finishing spec for epoch manager. Will work on combining state parts without using cache.

kpr: working on epoch sync. Will add block ordinal to `BlockHeaderInnerRest`, which is needed to check block merkle proofs.

Discussion: Adding secp key for validators and add bridge specific secp signatures to blocks. Bowen and Alex will write up a document detailing the changes necessary to share it with everyone on the team as well as the community.

## Dec 15, 2020

Michael: rerun the experiment for sharded network after fixes. Network can sustain up to 1000tps, but 2000 tps still does not work (some nodes get kicked out)

Piotr: finish adding performance metrics (PR submitted); improved stats by adding number of blocks that need to be synced; finish adding metrics for memory behind a feature flag; figured out issue with intellj

Misha: fix the cache contention on trie view cache; found the issue that we don't check version of header and block; state part request is slow (traverses the trie in order) and may not benefit from caches. Plan to disable cache for that. Also looked into lock-free lru cache, but didn't find anything in rust

## Dec 08, 2020

Michael: fix performance issues when found during sharded network investigation. Check header signature only once; only apply blocks when it has all chunks

Piotr: Adding performance metrics: see how much resource each actor uses by adding wrappers on top of actor. Currently measure only cpu usage. Going to print from one thread vs. every thread. Working on improving memory investigation tools. Going to investigate why we have more futures every day. Change info logging to print number of blocks that need to be downloaded.

Misha: Opened PR for approvals change; found weird bug in header sync/state sync; investigate node hanging. Looking into lock contention on view cache in trie. Seems like we can fix the lock issue by ignoring invalidated view cache since view client can fail safely.

## Dec 01, 2020

Michael: Didn't find anything wrong with routed messages. Sometimes response is dropped. PR for re-enabling seals

Piotr: adding performance metrics to actix actors. Only 6MB/day memory leak: in spawned futures & actix. Wrote documentation on improving compilation time

Misha: Finish the orphan fix: more invasive than expected. Will help unblock addressing `is_height_processed` issue. Also investigate the issue with node hanging.

Alex: no update on syncing. It is however a nonprotocol change.

## Nov 24, 2020

Michael: sharded network investigation w/ transaction loaded. Latency seems be in the distribution of chunk parts. Chunk part forwarding does not help enough. Bottleneck is mostly in getting chunk parts.

Piotr: Fixing the security issue by limiting the number of total connections (including pending ones). Modifying meory leak detection. Hopefully will get more data by the end of this week. Document how to improve rust compilation speed without changing Cargo.toml.

Misha: Investigating potential deadlock. Didn't find any potential deadlock in our code. Keep working on fix for orphan pool.

Bowen: dealing with critical issues on mainnet. 

## Nov 17, 2020

Michael: still investigating the 100 nodes, 8 shards network: sometimes it takes a lokng time between receiving a block and producing the next chunk.

Piotr: No memory leak for wasmer and rocksdb. Reduced rocksdb cache size to 2GB. Still investigating whether there is memory leak in rust code.

Misha: pushed partial fix for orphan attack and submitted a PR for chunk mask. Continue working on fixing orphan attack. Investigating is_height_processed issue.

Alex: still working on syncing

Bowen: investigated some performance issue network; recalibrating inflation

## Nov 10, 2020

Michael: unexpected node behavior in 100 node, 8 shards network. One node behaves abnormally (takes a long time to receive blocks at some point). Need more debugging info to figure out what happened.

Piotr: Try to reproduce wasmer memory leak by running example contract in a loop but this fails to reproduce the issue. Pinned down the exact data structure in
wasmer that leaks memory. 20% of the leaks have been fixed by the disk cache change. The remaining leak is related to mmap used by wasmer. Easy to reproduce if run a node.

Misha: Investigating is_height_processed usage. Working on orphan attack fix.

Alex: 1) change the interface for fetching light client blocks while maintaining backward compatibility. Working on database migration for the change 2) garbage collection of headers & light client headers. Garbage collection of headers is done and light client sync is in progress.


## Nov 03, 2020

Michael: sending 50k tps to a network of 100 validators and 8 shards and it is able to maintain 5k tps. For 5k input tps, added chunks counting and it is always 8. For validators getting kicked out under no load, adding more logging to figure out what happened.

Piotr: Trying to reproduce memory leak with wasmer. Starting with wasmer example and compile it in a loop but didn't find any memory leaks. Currently trying with one of the contracts we use to see if it can help reproduce the issue.

Bowen: Finishing introducing nightly protocol features and transition the upgrade processs to use nightly protocol features

## Oct 27, 2020

Michael: figuring out bottlenecks around sharding. Time from sending parts to parts get validated suddenly spiked at 20 shards. Data received by nodes piked at around 15-16 shards. Look at 8 shard tps.

Piotr: optimize tools to make sure node can sync with the debugging tools running. Half of the memory leak comes from rocksdb. Wasmer also constantly leaks memory. Trying to repro the wasmer memory leak. Changing `max_open_files` in rocksdb only makes it worse.

Alex: light speed sync & garbage collecting headers

Bowen: Test nightly protocol. Fix order of execution outcomes.

## Oct 20, 2020

Michael: merged version shard chunk header. Working on post phase 1 branch. Fixing some pytests

Piotr: finish the first iteration of memory debugging tools. Can see the allocations in the C code. Memory report for rocksdb is incorrect

Misha: working on state sync. Making sure we can combine state parts when they are large.

Alex: rewriting sync code: light client sync and garbage collecting headers

## Oct 13, 2020

Michael: version shard chunk header. fixing test

Piotr: measure memory usage from libc. Jemalloc only shows stats for rust code. Writing a tool to dump memory from a process. Need to figure out the code that allocates the leaked memory.

Misha: fixed applying genesis state on node restart

Bowen: Investigated and fixed lock contention on trie cache; fixed the issue with execution outcome on forks so that the execution outcomes on canonical chain cannot be overwritten.

## Oct 06, 2020

Michael: version shard chunk header. Almost done

Piotr: finish adding jemalloc stats per thread. most of the leak is outside of rust code. Leaks happen mostly on the main thread. Peer manager is also leaking memory. Investigating leak in C code

Misha: finding possible attacks.

Alex: fix stress.py. In doing so also address the issue of requesting chunk parts from nonvalidator nodes.

Bowen: Return receipts from transaction status query rpc.


## Sep 29, 2020

Marcelo: Fix several issues with network. Fix massive state sync test. Implement achival peer anchoring

Piotr: Debug memory leaks. Add jemalloc stats. Counting memory allocated per thread. We use 20MB for some peer actors. Add cache size counting. Rocksdb reports using 0 in block cache.

Michael: working on merging post-phase-1 branch. Need to figure out how to add protocol version to ShardChunkHeader.

## Sep 22, 2020

Marcelo: working on fixing massive state sync test.

Piotr: Investigating memory leak. 500 MB out of 1.8 GB is used by rocksdb. Need to look into memory used by wasmer. Investigate actix memory usage

Alex: Syncing from archival nodes. MOONGLOW

Bowen: Testing syncing from archival on mainnet. Fixed several issues with block sync and partial chunks in storage. Fix an inconsistentcy with final state.


## Sep 15, 2020
Michael: fix issue with slowdown when there are many (10k) transactions submitted to the node

Marcelo: network upgrades, state sync tests, spec for new sync. Investigate memory spike during state sync

Piotr: procedural macros for measuring the size of the structs in memory. More investigation on memory usage

Misha: Fixed storage inconsistencies. Fixed a nightly test

Alex: Syncing for archival nodes

Bowen: remove holding genesis records in memory, get execution outcomes for chunks in view client


## Sep 08, 2020

Marcelo: Network protocol upgrades regarding the change of handshake format. Fixing state sync test

Piotr: debug memory leak

Michael: investigating the slowdown when we send transactions at 10k tps

Misha: investigate database migration failure and inconsistency in storage

Bowen: fix issue in upgradability. PR for enabling inflation

Alex: Ability for archival nodes to sync the full history of chain


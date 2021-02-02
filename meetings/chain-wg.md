# Chain Team Agenda and Notes

This thread contains agenda and/or summary of the regular chain sync meeting. Please propose agenda items through the PRs and Issues.

Things that the chain team work on include:
* Consensus
* Sharding
* Network
* Cryptographic primitives

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


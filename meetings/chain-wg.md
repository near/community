# Chain Team Agenda and Notes

This thread contains agenda and/or summary of the regular chain sync meeting. Please propose agenda items through the PRs and Issues.

Things that the chain team work on include:
* Consensus
* Sharding
* Network
* Cryptographic primitives

## 10.13.2020

Michael: version shard chunk header. fixing test

Piotr: measure memory usage from libc. Jemalloc only shows stats for rust code. Writing a tool to dump memory from a process. Need to figure out the code that allocates the leaked memory.

Misha: fixed applying genesis state on node restart

Bowen: Investigated and fixed lock contention on trie cache; fixed the issue with execution outcome on forks so that the execution outcomes on canonical chain cannot be overwritten.

## 10.06.2020

Michael: version shard chunk header. Almost done

Piotr: finish adding jemalloc stats per thread. most of the leak is outside of rust code. Leaks happen mostly on the main thread. Peer manager is also leaking memory. Investigating leak in C code

Misha: finding possible attacks.

Alex: fix stress.py. In doing so also address the issue of requesting chunk parts from nonvalidator nodes.

Bowen: Return receipts from transaction status query rpc.


## 09.29.2020

Marcelo: Fix several issues with network. Fix massive state sync test. Implement achival peer anchoring

Piotr: Debug memory leaks. Add jemalloc stats. Counting memory allocated per thread. We use 20MB for some peer actors. Add cache size counting. Rocksdb reports using 0 in block cache.

Michael: working on merging post-phase-1 branch. Need to figure out how to add protocol version to ShardChunkHeader.

## 09.22.2020

Marcelo: working on fixing massive state sync test.

Piotr: Investigating memory leak. 500 MB out of 1.8 GB is used by rocksdb. Need to look into memory used by wasmer. Investigate actix memory usage

Alex: Syncing from archival nodes. MOONGLOW

Bowen: Testing syncing from archival on mainnet. Fixed several issues with block sync and partial chunks in storage. Fix an inconsistentcy with final state.




## 09.15.2020
Michael: fix issue with slowdown when there are many (10k) transactions submitted to the node

Marcelo: network upgrades, state sync tests, spec for new sync. Investigate memory spike during state sync

Piotr: procedural macros for measuring the size of the structs in memory. More investigation on memory usage

Misha: Fixed storage inconsistencies. Fixed a nightly test

Alex: Syncing for archival nodes

Bowen: remove holding genesis records in memory, get execution outcomes for chunks in view client


## 09.08.2020

Marcelo: Network protocol upgrades regarding the change of handshake format. Fixing state sync test

Piotr: debug memory leak

Michael: investigating the slowdown when we send transactions at 10k tps

Misha: investigate database migration failure and inconsistency in storage

Bowen: fix issue in upgradability. PR for enabling inflation

Alex: Ability for archival nodes to sync the full history of chain


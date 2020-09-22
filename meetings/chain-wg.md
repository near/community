# Chain Team Agenda and Notes

This thread contains agenda and/or summary of the regular chain sync meeting. Please propose agenda items through the PRs and Issues.

Things that the chain team work on include:
* Consensus
* Sharding
* Network
* Cryptographic primitives

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


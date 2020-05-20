# Middleware Work Group Agenda and Notes
This thread contains agenda and/or summary of the regular Middleware sync meeting. Please propose agenda items through the PRs and Issues.

## 20.05.2020

### High-level goals
Urgent:
* Prepare contracts for the audit -- Evgeny K, Mikhail K, Alexey F, Willem W.
* Bridge -- Anton B, Max Z, Alex S, Evgeny Kapun.

Slightly less urgent:
* Blockers for Phase 1 (still time sensitive)

Not super time sensitive:
* Integrating epoch manager with standalone runtime.

### Bridge
* Anton B: Fixed block producer hash verification. Currently implementation of light client in nearcore does not match the spec. Alex S is working on making it match;
* Anton B: Alex S wrote new version of light client specification, Anton B updated the contact code, but did not merge it yet;
* Max Z: almost attached the tester to Eth2NearProver. Will get rid Python in rainbowup, replace it with JS. Also, need to finish attaching NearRelayer to rainbowup.

#### Blockers
* The spec needs to match nearcore so that we can start testing NearClient for real. Need to sync with Alex S;
* Ask Alex S why his fix of receipts does not use the state root;
* ED25519. Anton B and Evgeny Kapun work on implementation. Evgeny Kapun is writing JS generator of Solidity code. The code seems to be almost working, approx 80% implemented. Need to sync with Evgeny Kapun;
* Nearcore RPC limit on query params prevents submission of the proofs.

### Core contracts
* Staking pool is done. Security audit company done reviewing it and they found critical issue in nearcore;
* Lockup contract implementation is done. Working on tests. Alexey F working on integration tests using stubs. Evgeny K is finishing the spec on how all of it works. Once it is done we will have another round of reviews and will send it tomorrow to the security audit company. Will also ask Mikhail K to review it. Logic is simpler now, since we delegated a lot of complexity to the staking pool;
* The next contract is whitelist contract. Foundation will be able to approve factories too;
* Voting contract;

### Status Report
* Alexey F: Working on lockup contract tests. Helped Anton B with debugging of the NearClient, related to endianness;
* Anton B: Blocked on the nearcore mismatch with the light client spec. Working on ED25519 with Evgeny Kapun. Finished some version of NearClient (that matches the current spec). Started migrating to the new version, but blocked by not having test data;
* Evgeny K: Lockup contract testing and spec. Addressing the comments from the security audit company;
* Bowen W: Adding epoch manager to the host functions. In the voting contract added the context to the voting result. Addressing vulnerability find in nearcore;
* Vlad F: Cleanup and breaking apart the primitives crate. Unboarded Bogdan to network indexer and fuzz testing for JSON RPC;
* Nikolay I: Upgrading Wamser to 0.17.0 and relevant changes to the error handling. Required upgrading Rust toolchain for nearcore. Familiarizing with Wasmer source code, especially around errors, so that we have good understanding of it.

### Contract runtime
Max Z and Nikolay I discuss this project and its scope. Will be centered around compilers (Wasm->native and Rust/AS/etc->Wasm), code preparation tools, language specific stuff.

### Notes
* Vlad F: Stumbled upon usage of gemalloc in primitives and it looked weird. In the past Mikhail K measured that this created improvement, but now we can measure the difference using CPU-precise params estimator. Surpirisingly, Nikolay I and Vlad F have different results on host functions that are not storage.
* Vlad F: We need to pin the exact Wasmer version;
* Bowen W: We need to make sure our protocol does not depend on Wasmer:
  * Do not have specific error payload in the protocol (brought by Alexey F);
  * Code prepartion tools, like gas injection are pwasm-utils;
  * The way we treat their erros is also generic code;
  * We need to make sure Wasmer correctly implements Wasm spec (We need to double check these tests, because they forked them from standard Wasm spec tests). Note from Alexey F: Wasm spec can actually be updated. We need to make sure they are in sync with updated. We also need to make sure they run spec tests for the singlepass backend (we need to check it);
* Nikolay I: We need to have a strategy on what we do if something in Wasmer breaks.

## 15.05.2020

### High-level goals

Urgent:
* Precise and good fees -- Nikolay I. Done;
* Prepare contracts for the audit -- Evgeny K, Mikhail K, Alexey F, Willem W.
* Bridge -- Anton B, Max Z, Alex S, Evgeny Kapun.

Slightly less urgent:
* Blockers for Phase 1 (still time sensitive)

Not super time sensitive:
* Standalone runtime -- almost done, except integrating epoch manager.

### Status report
* Nikolay I: Finshing the fees PRs + migration scripts. Discussions on the future of Wasm VM. Will migrate Wasmer to 0.17;
* Vlad F: RPC issues, runtime PR reviews. Working on publishing standalone runtime. We would need to publish crates like store, and primitives;
* Anton B: Neat2EThClient (solidity contract) -- challenges + lockup of bonds. Near2EthProver. Bowen W has prepared modification to our header+lightclient that was neccessary for Near2EthProver;
* Evgeny K: Added validation of staking keys to the runtime, that prevents locking funds of people in the staking pool -- exploit that was found by Mikhail K. Done with staking pool. Evgeny K has an idea to create a staking pool factory for people participating in Stake Wars. Working on lockup contract. Removing multiple access keys from the lockup contract, only one key now. Added special key for staking pool usage only;
* Alexey F: Added patches to the standalone runtime. Tests for the staking pool. Reviewed upgradability proposal. Reviewed PRs to contract tests by Willem W. Trying to understand state machine of the epoch manager. We need to ask either Mikhail K or Bowen W to write out the state machine;
* Willem W: Helped with tests for the staking pool contract. Tests for the fun token. Helped review the spec of the staking pool. Now working on the spec of lockup contract;
* Max Z: debugging Eth2NearProver. Communicating gitcoin security and other bounties with applicants.

### Notes:
* 15 min meeting about receipt congestion next week;
* We need to ask either Mikhail K or Bowen W to write staking state machine spec;
* Alexey F suggests to fuzz test staking pool and make sure everyone can withdraw money from staking pool after it was called with a bunch of random staking calls.
* Alexey F is concerned about accumulation of rewards in the staking pool. Bowen W replies that reward can not go negative, but stake can decrease. Stake cannot decrease until we get slashing and because staking pool is upgradable this is not a concern.
* Bowen W: we need to price epoch manager host functions. Max Z: let's overshoot and discuss offline. Later integrate it into Nikolay's tool.
* Vlad F: Onboarding Bogdan to work on network indexer, which is a new crate for nearcore.

### Initial contract
* Fun token:
  * Tests -- about a day + half a day. Assign to Willem; 
  * Spec -- was already done. About a day;
* Staking pool:
  * Tests -- 2-3 days already spent (3 people). Most of the stuff is done;
  * Spec -- 2-3 days already spent (included generic staking pool spec). 2 people. Done;
* Lockup contract:
  * Tests -- no tests yet. Would take longer than staking pool, because it needs to integrate with staking pool, voting contract and whitelist. (We can do voting contract next, blocked by exposing epoch manager data in PR); Bowen W will take by EOD to expose neccessary stuff for the voting contract. Evgeny K: 5 days for 3 people worth of work;
  * Spec -- Willem W will be working on it. 2-3 days of work;
* Voting contract:
  * Implementation -- hard to test without host function. 1-2 days more after host function are exposed;
  * Tests -- 5+ days for testing;
  * Spec -- Bowen W: mostly done, some discussion is needed. 1-2 more days.
* Whitelist contract:
  * Implementation -- about 1 day 1 person for simplest implementation.
  * Tests -- 1 day for tests;
  * Spec -- 1 day for spec.

## 06.05.2020

### High-level goals
* Prepare contracts for the audit -- Evgeny K, Mikhail K, Alexey F, Willem W.
* Bridge -- Anton B, Max Z. Sync with Alex S.

Slightly less urgent:
* Blockers for Phase 1 (still time sensitive)

Not super time sensitive:
* Standalone runtime -- Alexey F, Vlad F, Willem W

### Status report
* Evgeny K: Staking pool contract, added owners method, can change staking key, added reward so that owner can receive fees. Deployed staking pool to betanet, and currently testing it with Stefano. Staking pool should be ready for the audit on Monday. Might need to simplify the staking pool for make it in time for Monday;
* Anton B: Switched from EthProver. EthProver has some issues with validating nodes of the trie. Switched to NearClient, read already the spec of the Light Client. Updated all the structures that are Borsh deserialized. Partially implemented light client verification;
* Alexey F: Working on the standalone runtime. Reviewing staking pool contract and its tests. Implemented view account and call method for the standalone runtime. Updated the issue of execution error contributing to the merkle tree;
* Vlad F: Firefighting with explorer, domains, docs. Discussed design of the standalone runtime with Alexey F. Working on designing network indexer for explorer, and discussed it with Bogdan. Worked on issues with telemetry. Community support for RL1;
* Max Z: Prepared with Wasm for Blockchain. DevOps with bridge, looking at the EthProver. Will probably repurpose open-ethereum code;
* Bowen W: Issue with error handling (https://github.com/nearprotocol/nearcore/issues/2536) that needs to be fixed, needs help from Alexey F. Alexey F: issue needs to be more well-defined. Vlad F: We need to write down the specific problem that Applayer has and solve it. There is also issue with sending lots of staking transactions. We need to brainstorm the solution.

### Notes
* Evgeny K. Corner cases need to be tested through standalone-runtime because it gives control through context;
* Max Z needs to fix issue about deleting account.

## 29.04.2020

### High-level goals

* Core contracts -- Evgeny K
* Contract testing infra -- Vlad F, Willem W, Alexey F
* Bridge -- Anton B, Max Z
* Safe fees -- Nikolay I

### Status report

* Anton B: Solved problem with deserializing Ethereum structures. Working on Merkle Patricia Trie proofs, hope to finish it this week. Asked around whether anyone worked on it before;
* Max Z: rainbowup sort of working. Should be usable to debug bridge by the end of this week + weekend.
* Nikolay I: Wrote PR to replace fees, they now use multiple threads. Everyone should checkout the blog post https://nearhack3r.blogspot.com/2020/04/precise-gas-metering.html?spref=tw ! Interview with Richard D;
* Evgeny K: core contracts. Staking + testing should be done this week. Lockup should be tested next week;
* Alexey F: working on runtime standalone. Error handling was not properly propagated to nearlib. https://github.com/nearprotocol/nearcore/issues/2518 ;
* Vlad F: Not much progress on standalone runtime. Cleanup of nearcore project overall, e.g. duplicate dependencies. Project restructuring. No outstanding RPC issues right now;
* Willem W: Ported Evgeny's Python script for cross-contract testing to TS. Working with Sherif on AS education material and documentation. After prep for RL1 will return to runtime standalone. AssemblyScript Community Group meeting.

## 22.04.2020

### Agenda

* Vlad F: Finishing RPC fixes, working on dependencies (we have outdated deps and libs with duplicated functionality), had a sync with Willem and Alexey on runtime standalone. Re dependencies -- trying to figure out the high-level picture of the standalone runtime;
* Alexey F: Added ExceptionCodes back. Storage code cleanup. Helped people in near dev chat. Removing actix from primitives;
* Anton B: Testing Ethprover of bridge, lots of complexity with serialization on proofs. Close ot find and debug bugs in proof algorithm; Will take 1-2 days more to debug. Basically translating code written in Python into Rust. Moving tests from inside Wasm contract code into tests running against nodes, had to clean up devops for that;
* Max Z: rainbowup --  still WIP. Organizing Wasm for Blockchain CG and session for RL1;
* Evgeny K: Initial contracts. Refactored the repo, pushed lockup contract, then also pushed Bowen's contract. We have to redesign staking contract, because it takes non-const time per operation. Lockup contract is kinda done, in terms of integration with staking contract, but it does not verify the balances (e.g. when contract is terminated and balances cannot be moved). Trying to simplify voting contract. Max Z: We need some good documentation after we are finished with the code; Stefano is working dApp for staking/delegation (frontend for the staking contract). Also, working on the app -- WebRTC-based video conferencing app;
* Nikolay I: Resurrected cost estimation app, lots of broken stuff. Attached QEMU, required writing plugin for QEMU. It can now measure costs in CPU instructions. Some operations are precise up to single CPU instructions. See pending PR. Also had to fix several bugs in QEMU emulator, resulting in reports and patches. Everyone else could take a look at it, there are some nuances with threading;
* Willem W: Pick bugs in near-sdk-as. Set up proper publishing github publishing actions for the standalone-vm. Importing Evgeny's python scripts to JS;

## 15.04.2020

### Agenda

* Max Z: Fixing expensive tests, will start working on Eth-bridge DevOps. Anton B to share instructions on how to start the bridge;
* Anton B: Eth bridge DevOps, problems with returning errors from smart contract. Plans to continue testing eth-prover;
* Nikolay I: Finished with the voting app (DevX to massage it), now working on cost-estimation tool. Had to fix it first. Current plan to use QEMU to count actual number of CPU commands in user-level mode. Don't forget to disable caching in nearcore (see compilation flag);
* Evgeny K: Helper functions for near-sdk-rs. Started working on combining lockup and vesting in one contract, make vesting an optional feature. Some questions on how to handle Illia P's staking contract API, mostly around voting and delegation. Voting design is unclear, because if there are many validators the function can just fail due to gas. Also working on the token factory, mostly done right now;
* Alexey F: near-sdk-rs fixing checks for payable view functions. Wasmer update for nearcore;
* Vlad F: Small items on RPC side. Some discussion on backwards compatibility with Vlad G, e.g. about passing u128 through u64. Probalby u64 will be soft-deprecated with u128 string. Working on the standalone runtime, looking at the dependencies.  using tools for dependency inspection. Discovered lots of issues with our dependencies, like different versions. Will try introducing check on CI that makes sure we don't have stuff like this.

### Notes

* Add password to Zoom links;
* Alexey F: Not sure we need to store error content in the block, maybe we should just store the fact that error happened. It prevents us from improving on the debugging information;
* Nikolay I: We need to include all contract dependencies into near-sdk-rs.

## 08.04.2020

### Agenda

High-level focus, three major work items:
* Wasmer error handling;
* Disabling transfers, genesis contracts;
* Eth-bridge -- Anton with someone for DevOps;


* Alexey F. Vlad F. Will help Willem W with cross-contract testing;
* Evgeny K: Lockup/vesting contract, voting contract, Python testing infrastructure. Can also take the staking contract testing from Illia P;
* Vlad F: Finishing some RPC and DevX items, helped Nikolay I;
* Anton B: Needs some DevOps help, and more help. Need an equivalent of nearup;
* Nikolay I: Will wrap up voting contract in about one day;
* Max Z: Wasmer errors, disabling transfers.

Notes:
* Compiling standalone runtime to Wasm is not necesary for cross-contract testing of Rust contracts;
* Anton B says EVM is easy to implement, we should look at the 300 LOC project that implements EVM in EVM. Anton B to share a link;
* Alexey F says there is a spec test for Wasm we need to make sure Wasmer singlepass backend passes it.

## 01.04.2020

### Agenda
High-level focus is closing the Mainnet release.

CI is blocking people.

* Nikolay I: Getting up to speed reading docs. Developing voting app for the showcase (example) and for gas estimation. Ash mentioned that they would need voting app. Proposed name "Full Stack Voting App". Talked to a bunch of people: Alexandra, Ash, Vlad. Discussed Kotlin Native as a potential language for contracts;
* Alexey F: Upgrading Wasmer for runtime, looking at internal errors from Wasmer. Then will be looking at #1934;
* Evgeny K: Disabling top-level account registrations, need to wait to Applayer to flip the switch in the config. Need to make sure Applayer understands this is a Mainnet blocker. Also working on storage refactoring. Might not pursue it and consider hashing of account ids to not be critical for the Mainnet. After that will be working on lockup/vesting contracts.
* Vlad F: Limiting the max lenght of b58 encoding on RPC, finished a few doc items with Sherif (mostly done with documenting of experimenting APIs). Finishing the remaining RPC items, including one from misterwhite;
* Max Z: Working with Willem on cross-contract testing. Will be workign on bug with integration test #2208. Disabling transfers. TODO: Follow up with Wasmer on differntiating host panics.
* Anton B: Ethprover fixes, light client;


## 25.03.2020

### Agenda
High-level focus is closing the Mainnet release.
Story points:

Max Z - 18. 2 story points a week.
Evgeny K - 31. 4 story points a week.
Vlad F - 6 + explorer + block production debug
Alexey F - 10

* Evgeny K: Refactoring of TrieKeys, not sure we can do accout hashing without iterators. Vesting contracts are blocked on staking contract (Illia P is working on it). Token standard is done and in review;
* Nikolay I: Onboarding, getting familiar with Rust&codebase, and looking into fee estimator. Alexey F can hehlp with runtime questions;
* Alexey F: Fixing panics in runtime storage, the other issues assigned in Zenhub;
* Vlad F: In nearcore, b58 decoding issue. Cleaning up RPCs. Misterwhite's request to have different error codes;
* Anton B: Spec, Ethprover fixes, light client;
* Max Z: Disabling transfers, blocked by staking delegation (Illia P). Genesis content. Other things from ZenHub.

## 18.03.2020

### Agenda
High-level focus:
* Evgeny K: Vesting contract (vesting and lockups are two contracts now. Lockups are missing tests), Borsh perf improvements (almost done), updated token standard, Hashing of account ids and columns changes (stuck on migration script. Need to sync up with Bowen W);
* Anton B: Spec, Ethprover fixes, light client;
* Max Z: Learning eth-bridge, debugging EVM;
* Vlad F: Finished P0 for misterwhite, working on P1 issues for RPC label (reviewed by Evgeny K). Adding regression tests for RPC (changes API is taking a lot of time). Illia P brought up seat price in RPC. Benchmarking of RPC;
* Alexey F: Finished early version of runtime documentation. Still working on 1131, 1804, 1795.

### Notes
* Please comment on https://github.com/nearprotocol/NEPs/issues/40 ;
* Evgeny K: we need to come up with the process on how we propose and argue about NEPs. Checkout bitcoin proposal guidelines. It should be referring to our core principles and goals;
* Vlad F noticed that queries that touch non-tracking shard are extremely slow;
* Alexey F suggests that the refund is using current gas price instead of gas price attached to the receipt. Alexey F needs to create an issue.

## 11.03.2020

### Agenda
High-level focus:
* Evgeny K: Vesting contract, Borsh perf improvements, Hashing of account ids (some forward compatibility changes), columns changes (since we have fixed size keys now);
* Anton B: Working on spec with Sherif https://github.com/nearprotocol/NEPs/pull/38, verifiers QA (Ethprover), light client;
* Max Z: Turning off iterators. Light client and chain stability;
* Vlad F: Documentation for changes API https://github.com/nearprotocol/docs/issues/173 ; P0 changes for misterwhite (expose touched accounts in the block); further improvements of changes API to allow batch request for list of accounts (less critical). Needs to test through Python integration tests;
* Alexey F: deduct used gas at the very end of contract execution (also double check that our promise fee remains correct). RPC issues;

### Notes
* Anton B, Evgeny K: Multicontract requires scope that's too large and runtime is currently frozen. Let's revisit after the launch;
* Anton B, Alexey F: Let's consider instantiation ids for the contract given upon deployment, similarly to create2 in Eth. Let's revisit if after the launch. This will enable us the "sharded" contract architecture. Create commonwealth thread or NEPs;
* Vlad F: Too many work items. Alexey F to help;
* Alexey F: An idea to have a global gas counter living through the entire transaction/receipt lifetime;
* Vlad F, Alexey F: We don't need to expose used gas, because error messages already expose them;
* Evgeny K: Created PoW faucet, see https://near-examples.github.io/pow-faucet/ ;


## 04.03.2020

### Agenda

High-level focus:
1. Evgeny K: Making sure state iterators are working correctly and hide them from the contracts (some changes needed for AS), vesting contract (requested by misterwhite);
2. Anton B: Working on Eth-bridge with Sherif, more testing for eth-proof after cross-contract calls change by Evgeny K, finishing token example, fixing CI (for now omitting MacOS because of the docker problem);
3. Vlad F: Finishing misterwhite P0s (had to bulk multiple issues in one fix, which delayed P0s), wants to implement test suite in Rust (Python test suite is not flexible enough for changes API), maintenance of explorer;
4. Alexey F: working on 1131, 1804, 1795, also re-examining every error in the runtime, compiling list of scenarios for each error;
5. Max Z: Addressing Evgeny K' comments on metadata and Borsh schema, and ramping up on the chain stability issues;



### Notes

We have de-prioritized everything non-critical in runtime, including:
* Safes: https://github.com/nearprotocol/NEPs/pull/26
* The following storage rewrite: https://github.com/nearprotocol/nearcore/pull/2050



Explorer:
* We want to create business opportunities for people to build explorer-like backends on us;
* Ideally, we should not be running publicly available explorer backend because people will start building dependencies on it, and which would require from us guaranteeing the uptime. However for now it is fine to make it publicly available, and we will decide later what to do once backend is starts getting spammed.

## 26.02.2020

### Agenda

High-level focus:
1. Anton B: Eth bridge, adding token examples, fixing verifiers, contracts calls, documentation;
2. Evgeny K: Improving contract development experience: contract examples, gas profiling, safes (Alexey F), adding more features to near-bindgen (e.g. no std);
3. Evgeny K, Alexey F: Revisit runtime label on Zenhub;
4. Max Z: Borsh schema needs to be adjusted according to Vlad G comments. Need help from Applayer;
5. Mikhail K: Rewriting and stabilizing state storage. Max Z and Vlad F to onboard;
6. Vlad F: RPC/API issues with documentation;
7. Vlad F: Explorer backend/Node indexing discussion (Note from Anton F. Might address data-availability issue).

## 19.02.2020

### Agenda

High-level team focus:
1. Improving safety, DevX, performance of the Eth<> Near bridge;
2. Improving contract development experience: safes, contract examples, gas profiling, adding more features to near-bindgen (e.g. no std);
3. Introducing Borsh everywhere, need help from Applayer;
4. Rewriting and stabilizing state storage;
5. RPC/API issues with documentation.

### Notes
* Evgeny K: Need some fixes for Applayer as part of (3);
* Alexey F: Users do not know what is happening to transaction after they have submitted it. Max Z: Look at https://github.com/nearprotocol/nearcore/issues/1880 and https://github.com/nearprotocol/NEPs/pull/1 ;

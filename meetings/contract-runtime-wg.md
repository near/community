# Contract Runtime Work Group Agenda and Notes
This thread contains agenda and/or summary of the regular Contract Runtime sync meeting. Please propose agenda items through the PRs and Issues.

The scope of the contract runtime is the following:
* Wasm execution on the host:
  * Wasm VM and glue code around Wasmer+singlepass;
  * Code preparation tools, like gas injection and static checks;
  * Approaches to efficient caching of compilation;
* Gas metering:
  * How to make it faster while keeping safety;
  * Platform-independent gas fees estimation for Wasm operations;
* Host-guest interfacing:
  * How to pass complex structures efficiently between host and guest;
  * Contract-language specific mechanisms, e.g. Rust/AssemblyScript glue code needed to communicate across boundary;
* Rust/AssemblyScript to Wasm compilation:
  * Hiding low-level Wasm and glue code behind high-level contract interface. E.g. macros for Rust, compiler hooks for AssemblyScript;
  * Language-specific structure of the contract, how developers indicate contract methods, contract state, etc;
  * Best practices and the guidelines for contract development and compilation. Guidelines for optimizing contract for: Wasm size, gas usage, safety.

Short- and medium-term goal: Safe and highly performant contract execution runtime for Near;
Long-term goal: Near-independent and non-specific to Near contract execution runtime for general blockchains.

## 15.07.2021

Done:

* Code cleanups: https://github.com/near/nearcore/pulls/4488, https://github.com/near/nearcore/pulls/4516, https://github.com/near/nearcore/pulls/4481, https://github.com/near/nearcore/pull/4511.
* Proposal for wasm32 based contract unit tests: https://github.com/near/near-sdk-rs/issues/467

In Progress:

* storing gas profiling metadata in the ExecutionOutcome: https://github.com/near/nearcore/pull/4466
* wasm-global based gas metering
* improving wasmer 0 deserialization performance by switching to borsh

## 08.07.2021

Done:

* Measured gas metering overhead for various kinds of contrats

In Progerss

* Helping transaction runtime with shipping contract compilation on deployment
* Fixing storage corruption that prevent the migration that introduce execution outcome metadata (https://github.com/near/nearcore/pull/4466)
* Revive borsh support for wasmer 0 (https://github.com/near/nearcore/pull/4448)

## 17.06.2021

Done:

* patch state(sandbox) PR merged, docs updated
* fixed some bugs in near CLI
* https://github.com/near/calibrator to estimate preformance of validator node
* explaind IO benchmark results

In Progerss:

* confirmed gas counting overhead with real-world benchmark (EVM contract)
* some progress on adding import support wasm-smith


## 10.06.2021

Done:
  * reproduce macro migration benchmark locally, cross-validated gas costs via perf
  * lockup contract auditing

In Progerss:
  * fuzzing infrastructure to exercise host functions
  * near sandbox API for patch state. 

## 27.05.2021

Done: 
  * removed ExecutionOutcome from PeerMessage, so that we can change it
  * sorted out lost debug logs in near-vm-runner
  * doc the usage of sandbox node with near-api-js for e2e test and usage of patch_state rpc
  * fix a near-api-js bug during the doc https://github.com/near/near-api-js/pull/603
  * publish the design doc on gov.near.org: https://gov.near.org/t/design-doc-of-near-sandbox/2264
  * addressing comments on state patch rpc pr
  * fixed wrong error handling logic with wasmer 0.17


In Progess:
  * looking into wasmer reliability
  * more work on gas & time profiling


## 20.05.2021

## 13.05.2021

Done:
  * hot-fixed 1.19 to treat FailWithNoError as deterministic
  * (almost) cost estimation for wasmer1 and precompilation is ready to be merged
  * Gas profiling/breakdown is enabled by default. 

In Progess:
  * investigating signal handlig behaviour in wasmer 0.17 and 1.0
  * design truffle/ganache like approch for contract testing. Desing is finalized, first WIP PR is up #4287.
  * tool for measuring gas and runtime costs *simultaneously*, to verify our estimations #4295
  * look into wasmer 0.17 signal handlig code more.

## 06.05.2021

Done:
  * wasmer team implemented fast rkyv-based serialization for jit https://github.com/wasmerio/wasmer/pull/2250, for 30-40% deserializaion perf improvement. 
  * design for new ganache-like approach to contract testing
  * merged configurable staet size limit for trie viewer: https://github.com/near/nearcore/pull/4199

In Progess:
  * cross-validation for compilation costs estimates
  * EVM contarct profiling


## 29.04.2021

Done:
  * #4221 expose epoch height via rpc
  * merged rkyv support in wasmer for native (not for jit, which we actyally use)
  * add single threaded option to wasmer
 
In Progerss:
  * state loading for sdk-sim
  * design for sdk-sim-cli
  * estimating wasmer1 and precompilation costs (fixed obvious bugs, need cross-validation)
  * add tracking issue for missing costs, to know where to stop


## 22.04.2021

Done:
    * precompilation merged (off by default)
    * done local reproduce any testnet contract call (need explorer team coop to ship)
    * refreshed developer docs
    * reviewed architecture of the sdk&simulator, there's a plan on how to make developing them easier

In Progress:
    * recomputing costs with Wasmer1 and precompilation, while fixing the computation itself
    * adding contract reproduction features to the simulator
    * adding rkyv support to wasmer (final rounds of review)
    * wasm fuzz tests
    * discussion: should  we profile gas by default?


## 15.04.2021

Done:
    * Error handlid rework https://github.com/near/nearcore/pull/4181
    * Basic example for local contract reproduciton works! https://github.com/ailisp/repro-near-funcall, #4203
    * Adding state-file option to vm standalone: #4209, we are actually using non-trivial state now!

In Progress: 
    * Contract precompilation https://github.com/near/nearcore/pull/4205
    * Expose enough information via rpc for reproduce contract execution: #4192 #4199 #4221 #4222 (work done, waiting for review)
      created an issue on explorer for public node interface team to expose these endpoints on explorer UI
    * Contract runtime developer documentation (waiting for review)
    * Exposing configuration parameter to enable gas profiling at runtime
    * Unbreaking params estimator

## 08.04.2021

Done:
    * EVM contract benchmark
    * Synced  with node interfaces about reproducible contract executions
    * CLI for state viwer to dump contract state for reproducing contract execution

In Progress:
    * Wasmer error reworking
    * Compilation on deployment
    * Exposing configuration parameter to enable gas profiling at runtime
    * Re-run param estimator with wasmer 1.0 to makes sure that it's not worse

## 01.04.2021

Done:
  * preloading and pipelining are done, but not integrated. 
  * Wasmer 1.0 failure turner out to be ops related 
  * native + singlepass benchmarked to be not so much better than JIT 🎉
  * we have better mental model of performance

In Progress:
  * Wasmer error reworking (to distinguish between deterministic & non-deterministic errors)
  * EVM contract benchmark almost done
  * rkyv supoort in wasmer (need to move to JIT?)
  * make costs profiling safe to enable by default

Planned:
  * simple compilation on deployment
  * reproduce particular contract execution locally from a transaction explorer 


### Status

## 25.04.2021

No meeting

## 18.03.2021

### Status

Done:
  * Testing wasmer 1.0 (https://github.com/near/nearcore/issues/4076).
  * Experimeted with flat data serialization mechanism for wasmer: rkyv deserialize or rkyv archive (zero-copy) show good perf improvements.
  
In Progress:
  * Investigate wasmer 1.0 failure on testnet
  * Experimetally integrate pipelined execution with runtime (goal: figure out actual speed up to understand 
    if this is worthwhile to try to lower gas costs this way).
  * Trying to use native compile + single pass with wasmer, needs work on wasmer side.
  * Memory and store re-use for pipeline execution
  * Investigate, why wasmer instantiation takes much longer in the cloud.

## 11.03.2021

### Status

In Progress:
  * Experimetally integrate pipelined execution with runtime (goal: figure out actual speed up to understand 
    if this is worthwhile to try to lower gas costs this way).
  * "Intergrated" benchmark for runtime.
  * Experimeted with flat data serialization mechanism for wasmer.
  * Testing wasmer 1.0 (https://github.com/near/nearcore/issues/4076).
  * Sorting out CI problems for std-rs. 

Done:
  * VM preparation pipeline (3x-4x speed up on a sythetic bench)
  * Updated vmlogic crate to use updated structures, i.e. `ProfileData`
  * Changing tests from integration test to unit test, so we build fewer artefacts
  * Experimeted with https://github.com/TimelyDataflow/abomonation serialization mechanism and storing Wasmer symbol table
    (some improvent, too unsafe)
  * Published near-core crates (still pre-release, but works!)
  * new near-sdk-as release :tada:

## 04.03.2021

### Status

Done:
  * Testbed capable to reproduce complex runtime behavior situations is ready: https://github.com/near/nearcore/pull/3983,
      scripted via Rust DSL
  * Clarified Wasmer team questions in wishlist
  * Similified feature used in few refactoring
  * Contract caching for simulation tests is done
  * Docs improvements in simulations tests
  * Preparing AS SDK prelease

In progress:
  * Investigating regression https://github.com/near/nearcore/issues/4030 fine tuning perf report infra
  * VM preparation pipeline, API reworked, some concurrency issues
  * Updated vmlogic crate to use updated structures, i.e. `ProfileData`
  * Changing tests from integration test to unit test, so we build fewer artefacts
  * Experimeted with https://github.com/TimelyDataflow/abomonation serialization mechanism and storing Wasmer symbol table

## 25.02.2021

### Status

Done:
  * Wasmer 1.0 support is finally merged
  * Willem's refactoring of crates is over and merged, working on publishing
  * Decided to avoid using Borsch in Wasmer by default
  * Added in-memory testing to simulation testing
  * Alex's tracing framework used in simulator
 
In progress:
   * First prototype of testbed capable to reproduce complex runtime behavior situations is ready (no caching support so far)
   * VM preparation pipeline, API is ready, implementation in progress: https://github.com/near/nearcore/pull/3985
   * Max suggests to remove in-memory cache for compiled contracts: to do after VM preparation pipeline
   * Got first feedback on wishlist by Wasmer team, mostly clarifications
   * Implemented initial prototype of VM in-memory whitelist: considering to move to "VM config contract" instead
   
Planned:
   * Optimyze.cloud offered their distributed profiling solution: decided we'll consider integrating their solution

## 18.02.2021

### Status

  Done:
    * Formed wishlist of features for Wasmer
    * Also created list of potential optimizations in our stack: https://docs.google.com/document/d/1PD9yRCs9PKkPLF-4jKLmAxbOKTH9xCEi7rd5o3RxUmM/edit
    * Added tracing support to runtime
    * Created unit test to reproduce EVM bridge perf issue
    * Discovered perf issue in simulator: was because of missing contract cache
  
  In progress:
    * Performance effort
    * Alex is working on testbed capable to reproduce complex runtime behavior situations
    * Investigated replacement of bincode with borsch, only 10% improvement, not go this way
    * Keep deserialized contracts in memory for now
    * Feature refactoring to simplify compilation in various configs and crates deps
    
 Planned:
    * Pipeline approach to execution: know all contracts in the block, preload all of them and prepare VMs 
    to run concurrently
    * Consider having more persistent contract cache for simulation testing, i.e. using RocksDB as BE
   
## 04.02.2021

### Status

Done:
   * Major crates refactoring
   * Fixed `cargo test` run in nearcore
   * Reintroduced memory cache for contracts
In progress:

   * Compile contract on deployment: https://github.com/near/nearcore/pull/3859
   * Wasmer 1.0 support merge is planned
   * Need to rework features related to cost counting
   * Reproducible environment for running perf critical contracts such as Eth bridge
   * Consider implementing WASM-level profiler
   * Wasmer 1.0 has dramatic deserialization performance improvements, even with bincode (15 -> 4 msecs), borsch can make it even better
   
 TODO:
    * Nikolay to take a look why we need BTreeMap in Wasmer and if we could reuse running VM
    * Bo to provide a profile of running contract to see the breakdown between parts (to reuse some profiling code from Analyzer)

## 28.01.2021

### Status

Done:
   * PR for switching Wasmer to Borsch serialization
   * Borsch support for usize

In progress:
   * Compile contract on deployment: https://github.com/near/nearcore/pull/3859
   * Compiled contract compression: got merged and reverted 
   * Major crates refactoring: almost there
   * Improvements of deserialization speed: maps and trees blocks

Discussion:
   * Diamond-shaped dependency graphs with different features cannot be solved by cargo
   * Both our target VMS have built-in gas counters (need to be consistent between VMs and current gas counters)

## 21.01.2021

### Status

Done:
  * IO costs logic https://github.com/near/nearcore/pull/3730 is merged
  * Wasmer 1.0 support mostly there: https://github.com/near/nearcore/pull/3799 (just to be merged)
  
In progress:
  * Prototype of syscall interface: https://github.com/near/nearcore/pull/3820 and https://github.com/near/near-sdk-rs/pull/272
  * Update of costs (with IO costs, better contract compilation cost): https://github.com/near/nearcore/pull/3818

Discussions:
  * Fix `syn` dependency issue in Borsch
  * Evaluate new costs for fixing https://github.com/near/nearcore/security/advisories/GHSA-q39c-4p3r-qpv3

## 14.01.2021

### Status

 Done:
   * Tried to compile nearcore on Apple Silicon hardware: fails so far (and no, Docker is not available)
   * Keeping profile data in ApplyState is merged: https://github.com/near/nearcore/pull/3730
   
 In progress: 
   * Adding Wasmer 1.0 support is WIP: https://github.com/near/nearcore/pull/3799, certain roadblocks:
      * Error info fields is non-public
      * Infinite loops not properly discovered by Wasmer compiler
   * Applying IO to costs is WIP: https://github.com/near/nearcore/pull/3730
   * Discussions with static analysis company (https://arxiv.org/pdf/1908.11227.pdf)
   * Discussions about AS and other non-Rust languages for contracts: https://gov.near.org/t/planning-for-assemblyscript-support-and-integration-for-the-next-6-months-starting-jan21/345
   * Simulation testing improvements per feedback from Mintbase
   
 Planned:
   * Large crates refactoring, to allow crates publishing
   * Simulation correctness improvements
   * Try nearcore build on Linux ARM machines
 
### Meeting notes

## 19.11.2020

### Status
  * P0 performance problem with view contracts was resolved: PR merged
  * Need to have "interface types" published in some crate to avoid "primitives obsession"
  * OKRs got synchronized with actual activities
  * Willem to take a step to get rid of inline asm in our codebase
  * Bo debugged estimator issue (see issue 3601), related to measuring of already deleted keys
  * After fix estimator runs too long, need to optimize run time (mostly related to number of accounts)
  * Bo will take a look on above optiomization
  * Willem finishing up simulation PR: addressing comments
  * Nikolay -> Willem as Rust SDK reviewer
  * Willem fixing deny.toml issues to simplify switch to stable Rust

## 12.11.2020

### Status
  
  * Memory leak seems to be resolved, thanks Piotr and Bo
  * Willem added docs to testing infra
  * Willem computed memory growth cost estimation
  * Willem took a look on using coverage in testing, evaluated DWARF support
  * Nikolay finished Contract WG OKRs and put those to the Airtable
  * Bo and Eugene is looking on estimator panic/hang
  * Bo debugging contract execution crash/misbehavior
  * Nikolay to look on issue from Eugene: we have P0 issue with RPC view calls not able to mutate compiled contracts cache: we could either mutate cache in 
    view calls or force precompilation on deployment across nodes (discuss with @frol or Misha)
  * Willem switched Rust/QEMU interface from inline asm to read syscall (3533)

## 05.11.2020

### Status

  * Finally got contract caching merged, few follow ups from Eugene and Bowen; Bowen resolved issue with migration
  * Contract caching PR also disable single-threaded mode of RocksDB, as non-functional
  * Bo will take a look on estimator run hang (with Eugene)
  * Willem's effort of compiling with stable Rust: dynasm and one inline asm in our Rust code: replaced inline asm with Rust operation
  * Willem: simulation testing wrapped up
  * Working on Contract Runtime WG OKRs: 80% done
  * Not much changed in memory growth cost
  * Wasmer memory leak wasn't reproducible, Piotr created an issue amnd let's see if could reproduce it
  * Wasmer planning no more 0.x releases, but could accept our patches to those branches
  * Eugene: we have P0 issue with RPC view calls not able to mutate compiled contracts cache: we could either mutate cache in 
    view calls or force precompilation on deployment across nodes (discuss with @frol or Misha)

## 29.10.2020

### Status

* Contract caching needs final approval from Evgeny
* Willem working on compiling Wasmer (and nearcore) with stable Rust: dynasm dependency of Wasmer need to be promoted, 
 lightbeam in Wasmtime seems to require nightly
* Nikolay performed first cost coefficient computations in https://github.com/near/nearcore/pull/3483, further
* Bo estimated EVM costs on multiple machines: results are stable
* Bo fixed EcRecover for EVM bridge
* Bo helping to look at Wasmer memory leak at https://github.com/near/nearcore/issues/3402
* Wasmtime depends on Lightbeam, which depends on static_vec, which doesn't work in Stable; To check if could use other backend, or could 
  use regular vec, and use own fork on Wasmtime.


## 22.10.2020

### Status

* Nikolay got cached contracts work close to the end: caching is working, key is Wasm code hash + VMConfig hash, we cache both code or compilation error
* Eugene made experiment with 10M contracts on SSD and RAM drive: no difference in measured data as expected
* Need to disable single threaded mode, as otherwise estimator cannot proceed
* Willem built vmrunner using Wasmer with dynasm dependency modified to not depend on nightly, and built with stable Rust (1.47), seems be the way to build
  nearcore with Stable, unless there are other deps
* Getting rid of in-memory caches for contracts Wasm bytes
* Test runner can run most contracts, can test cross-contract calls
* Eugene suggests to introduce feature discriminating actual contract compilation for deployment vs. tests running in Near SDK (now we check target platform)
* Bo corrected few costs where EVM interpreter wasn't started. EVM costs are now stable and PR is finalized and ready to land.
* Willem written the contract to measure memory growth cost, not yet integrated it into estimator
* Willem implemented ability to see all execution outcomes coming from a transaction

## 15.10.2020

### Status

* Nikolay implemented IO measurement mechanism in QEMU plugin
* Applied IO measurement tool to the genesis tool to see if dependency is linear, seems to be so up to 1M contracts
* Merged Bo's contract cache effort into Nikolay's PR
* `cargo test --all` mostly passes with caches, modulo transactioncs per second tests
* Bo did EVM cost estimation: machinery is mostly done, but function calls pretty volatile ~20%, other more stable ~3%
* Willem got testing infra basically working: including cross-contract calls
* Failed promises are not properly handled by the standalone runtime, to be fixed

TODO: Willem to complete memory growth cost computations

TODO: Willem to add ability to see all execution outcomes coming from a transaction

## 08.10.2020

### Status

* Willem working on memory growth cost computations, basing on insn count as function of requested amount of memory
* Macroses for the testing infra almost there: only final testing remaining
* Caching precompiled contracts. We need to decide on how to cache data: runtime shall be independent of DB service. Cache shall be
 trait on its own, not store trait. Shall cache be singleton, or explicitly passed to the runtime? We shall pass it to runtime's constructor.
* EVM cost volatility investigation: cost deploy varies, will increase number of contracts. EVM cost is not always linear from NEAR computed cost.
* Netlink approach for disk IO counting seems to work: so could use if for gas estimation
* Evgeny figured out RocksDB issue with huge number of contracts: as we disabled multiple threads, DB cannot flush the data without the background


## 01.10.2020

### Status
* Bo, did some work on volatility of the EVM cost, but haven't finished it yet. Make evm now count cost in the same way as near function calls (included cost to finalized block, deduct action receipt cost), it's more expensive than previously just counting function run_evm, in one number get can do 660 tps erc20 transfer under current block gas limit;
* Willem did a talk on wasm vs EVM on Friday that went well.  And the latest generation of the simulation testing now compiles, but it is just the hand crafted version so still need to turn it into a macro;

## 24.09.2020
### Status
* Nikolay Igotti is OOO this week.
* Precompilation with on-disk cache. Bo made a pull request for on-disk cache for precompilation, but Misha and Bowen are discussing what is the right way to do it. It currently uses Singleton. Misha suggested to do it from trie update by exposing store. Eugene suggests to introduce Singleton cache, which would also make it relatively easy to disable, we might also get rid of the `cached_key!` macroses, which are already hard to disable because we need to use 
compiler flags. We should address `cached_key!` later;

* Contract testing infra. Waiting on the next round of feedback from Chad and incorporated feedback from Illia. Trying to make it work without macros first. Not blocked on it though.

* Willem talked to Matt and created an AssemblyScript stripped down example to compare it to a similar EVM contract.

* Wasmer 1.0. We are waiting for Syrus to provide documentation on error handling.

* EVM fees counting are having some issues that Bo is working on. After Illia introduced new changes the code needs to be updated.
## 17.09.2020
### Status
* Filesystem-based contract precompilation using Wasmer code serialization
* First version of contract testing infra is here, got feedback fro Illia
* Still stuck with error handling in Wasmer 1.0 due to changed APIs
* EVM cost counting and deduction of gas cost in EVM by Bo: mostly done, needs review

TODO:
* Willem: address feedback for contract testing infra for cross-contract calls
* Willem: to take a look at https://github.com/nearprotocol/nearcore/issues/3120 https://github.com/nearprotocol/nearcore/issues/3122
* Bo + Nikolay + Evgeny: next step is to use database to store complied contracts, compile on deploy and recompute the fees
* Nikolay: evaluate approaches to accounting IO costs in gas price (3-4 days), discuss with Evgeny and Mikhail

## 10.09.2020
### Status
* Combined runner is functional, but we have certain conceptual discussions around, and not sure if going to merge
* Bo encountered issues with error mapping in Wasmer 1.0 alpha, no luck with running the runtime so far
* Willem can transform contract to expose its methods externally via macroses, PR 226 in Rust SDK ready for review
* Moving contract compilation from execution to deployment
* AS SDK will be published soon and moved mostly to bounties mode afterwards
* EVM gas cost estimation work by Bo

 Action items:
   * Bo attempt migration to Wasmer 1.0, blocked on Wasmer side so far
   * Write a full spec of Wasmer: provide functional tests and measure code coverage of VM code
   * Nikolay: disk performance must be accounted in fees

## 03.09.2020
### Status
* Nikolay is working on combined runtime. He got it working, but he is now testing. Bo and Willem are currently testing it;
* Bo is looking into Wasmer 1.0. They refactored errors;
* Willem workign on improving simulation testing. Added compilation feature to current macro called "simulation" that forces the bindings to be generated. Consolidating Illia P's code;
* Congratulations to Willem with his #10 AssemblyScript bounty!

## 27.08.2020
### Status:
 * Wasmer 1.0-alpha is released, plan to release regular version in timeframe about a month
 * Can start prototyping runtime with Wasmer 1.0
 * No success in getting rid of AVX in Wasmer, but estimate that as effort working week
 * Merged AVX conditional, can run estimators out of the box
 * Getting consistent results from param estimators (most fees in range of 1-2%, worst case is 20%)
 * Nikolay started combined mode of execution
 * Willem started working on Ganache-like testing environment for contracts
 * Willem merged improved compiler estimation, lightbeam shows better performance in compilation than singlepass
 * Willem tried fuzzing contracts to detect bugs in compiler

 Action items:
   * Bo attempt migration to Wasmer 1.0
   * Write a full spec of Wasmer? Maybe in form of tests
   * Param estimator is now running in cloud, sometimes hangs
   * Evgeny and Nikolay to work on proper definition of costs
   * Nikolay: disk performance must be accounted in fees

## 20.08.2020
### Status:
 * Gas profiler is merged
 * Profiler to be used in deployed nodes
 * AVX check yield problems in estimator, fix is ready (https://github.com/nearprotocol/nearcore/pull/3101)
 * Thread pool in RockDB can be turned, now estimators runs with threads disabled
 * Run with threads disable shown some variance 2-20% on some costs
 * Willem completing wasmtime support, added estimator support from running with Wasmtime and Lightbeam, costs with Lightbeam are comparable with Singlepass
 * Willem looked on using fuzzer to produce variations of existing Wasm binaries to test stability of compiler/VM
 * Stabilization of the contract runtime: Avocado
 
 Action items:
   * Take a look on Wasmer 1.0 (compiles with stable Rust)
   * Write a full spec of Wasmer? Maybe in form of tests
   * Nikolay: decide on how we don't blindly trust Wasmer, and get better safety
   * Bo set up cloud agent for cost param estimator, code written, need to create PR and add cron jobs, blocked on AVX checker
   * Evgeny and Nikolay to work on proper definition of costs

## 06.08.2020
### Status:
 * Continue gas profiler effort
 * Discovered inconsistent behavior of some nodes, theoretically attributed that to lack of AVX support
 * Added explicit check for AVX presence in case of using Wasmer, it broke parameter estimator, as QEMU has no proper support
 * Discussed with Wasmer on removal of AVX emission from Wasmer
 * Consider backporting it to 0.17.x
 * Evgeny did comments on costs meaning, Willem will try to adapt cost estimator accordingly
 * Willem did run of cost estimator on Wasmtime, it shows lower costs on larger contracts
 * Consider running both at the same time (need to plan for that; need assistance from transaction runtime)
 * Facebook refuses to merge PR with thread disabling to RocksDB
 
 Action items: 
   * Max to create an issue for NEAR-Ganashe effort, chain team will participate
   * Willem set up cloud agent for cost param estimator, code written, need to create PR and add cron jobs
   * Bo to check param estimator with threadless RocksDB
   * Evgeny and Nikolay to evaluate why function_call cost is so high

## 30.07.2020
### Status:
 * Analyzed the cost breakdown of Illia's contract, found issue in costs computation
 * Implement the cost profiler, in review
 * Meeting with partner about possibilies of getting help with nostd mode for Rust SDK, start with Borsch non-std
 * Contract runtime: discuss extending neard with built-in REST server, work as an application server, already exists in some form for chain testing
 * Willem is running cost estimator with Wasmtime to see how much VM affects the cost
 * Bo is working with ext contributor on threads disabling in Rocks DB, almost there, but in fork so far
 
 Action items: 
   * Max to create an issue for NEAR-Ganashe effort, chain team will participate
   * Evgeny will help to properly what is a definition of each cost
   * (Willem) set up cloud agent for cost param estimator

## 23.07.2020
### Status:
 * Bo, Nikolay are reviewing the cost estimation for Phase 1
 * Found one concurrency issue in estimator, fixed
 * Cost of function call is disproportional in current config (11k times difference)
 * Contract size optimizations:
     * submitted minifier tool to Rust SDK, contracts ~2x, but requires proof of correctness
     * raises the issue of testability and test coverage
     * decided as the next steps to focus on contract testability in standalone environment
     * Parity uses common crates to replace std collectors and allocations, cannot use dynamic strings
     * Compute contract size profile on every run of CI
 * Contract are similar to serverless functions/lambdas, and could provide REST APIs and use that for testing and deployment
 * @Max: we need to finish the standalone runtime for testing and contracts with REST APIs (Ganache from Etherium does the same)
 * Polish up devx story with testing/deployment: Vlad F., Evgeny, Willem, Alex F., focus for upcoming few months (Mocknet project could be similar)
     
Action item:  (Willem) set up cloud agent for cost param estimator

## 16.07.2020
### Status:
* Param estimator and threading: we cannot controil threads via Rust APIs, with C++ APIs there are more control, but still thread get created
  Resolution: estimate conservatively by upper bound, and get back after Phase 1, maybe by contributing to Rocks DB. Also consider disabling Rocks 
  DB cache during contract run.
* Contract size reduction:
   * looking at no-std mode. It requires serios SDK rework, and also we need to make sure no std functions used in contracts. Bo is looking at Parity, which uses no-std for contracts.  
   * Willem found that twiggy is rather good contract size profiler, shows top functions pretty well if names section is not stripped
   * from analysis with twiggy we found that significant part of many contracts is float to string convertor
   * Nikolay is looking at removing known unused functions with wasm-snip tool
   * Willem fixed twiggy to work with AssemblyScript
   * In AssemblyScript files string formatters are big as well
   * With Rust generics could be pretty big as well
   * Bo did currect example contracts size from Rust SDK run on CI
   
 Action item: look at nanoserde
 Vlad F: reproducability issues with Docker, hash maps make it less reproducible


## 26.06.2020

### Status report
* Nikolay Igotti: multi-VM support was merged, and we made it compilation-time dependency. Eventually this feature will become default, to make sure it does not break;
* Nikolay Igotti: There was a debug print out by Wasmer, we asked them to publish the version without it `0.17.1`. Nikolay Igotti will switch runtime to this version;
* Nikolay Igotti: Wasmer provided us with the access to the new `1.0` version, currently it is not published to crates.io yet;
* Nikolay Igotti: We postponed the whiteboard session with Runtime Verification to next Wednesday;
* Willem Wyndham: implemented the contract loading fee, but there is an issue with param estimator, see transaction runtiem meeting;

### Future focus
* Decrease the executable size. We will look at `no-std/alloc` for Rust API, and we will look at nanoserde. Also Nikolay Igotti contacted an independent security researcher to help us debug the size of the contract;

### Notes
* Max Zavershynskyi -- we need a docker image for building contracts. Might reuse the cost estimation image. Ask Bo Yao to push the image to our Docker hub. Let's ask Sandi to help with it;
* Nikolay Igotti: we need to understand how to run the contracts with more than one VM;
* Bowen Wang: Fees (by Willem Wyndham) is blocking us on checking upgradability, which we need for Phase 1;
* Bowen Wang: On code readiness we will be ready by mid July, but there are many logistic components that were not addressed yet;
* Nikolay Igotti: Will also schedule a whiteboard session with Cosmwasm.

## 19.06.2020

### Status report
* Nikolay I: We now have a functional version of contract runtime that can either choose Wasmer or Wasmtime. There is however a problem with running Wasmtime and Wasmer tests concurrently, since Wasmer sets the global signal handler which sometimes intercepts Wasmtime errors. Wasmer might handle it in the future. Overall, it is feasible to run two runtimes. Unfortunately, Wasmtime brought dependencies that pulled cmake version which breaks our CI. We also need to decide the mechanism of selection of the runtime;
* Willem W (by Nikolay I): Progress in introducing cost of contract exeuction derived from teh sie of the contract.

### External communication
* Planning to have whiteboard sessions with Cosmwasm and Runtime Verification.

### Notes
* Bowen W: This seems to be high priority. We need to make sure the same source code compiles into the same Wasm code. This might be urgent, to make sure our users can verify that given Wasm was compiled from the given source code. Vlad F: It seems Rust is already byte to byte reproducible for Wasm compilation. Max Z: We need to have a docker build image;
* Max Z: We might need to put more effort into reducing the contract size. We shoudl explore `no-std` mode for `near-sdk-rs`. Vlad F: there is https://doc.rust-lang.org/alloc/ crate that provides all collections and primitives for `no-std` mode in Rust. Alexey F: Regarding Willem's proposal to have std as a hardcoded Wasm library that we link against, it is not clear what will be the overhead of calling it using host functions. Evgeny K: We are using `-z` option which is supposed to reduce the size but for Wasm `-s` is often better. There is also a lot of debug data that accounts for ~15% of code size. Max Z: Suggest to checkout https://rust-embedded.github.io/book/ which is a guide to writing no-std Rust;

## 12.06.2020

### Status report
* Willem W: Added two new fees to the config: cost of loading in the contract, cost of compiling the contract. Max Z: suggestion: base cost of compilation/loading, cost of compilation/loading per byte;
* Nikolay I: Experimented with adding Wasmtime, had some issues with host functions. Max Z: AFAIK Nikolay I was able to work around the lack of unsigned numbers in Wasmtime.
* Willem W: Finally tied version of AssemblySript to near-sdk-as. We will now better reproducibility for the contracts. Batch promises were added. We currently have UnorderedMap (no specific order guarantees, because of the internal swapping). We will have OrderedMap (based on AVL). Max Z: Suggestion, let's have all collections that do not go into near-sdk-* in separate library/crate. Vlad F: let's have AssemblySript-like Map in near-sdk-rs, instead of direction operations with storage.
* Vlad F: There was a community discussion of the Debug trait in near-sdk-rs, that prevents people from autoimplementing Debug on user-defined types. Unfortunately, for collections Debug will fetch all items which is very expensive.
* Bowen W: there is a discrepancy between near-sdk-rs and master node.

Discussed adding releases and change log for: near-sdk-rs, near-sdk-as, borsh, runtime.


## 05.06.2020

### Current focus
* Adding another runtime -- Wasmtime to check consistency of Wasm execution. Nikolay I had a lot of progress prototyping binding Wasmtime with our node. Unfortunately, there is a critical bug in Wasmtime which prevents of calling our host functions. Wasmtime can only handle signed types in the their interface, while we use unsigned types. The integration seems to be feasible, so far. We need to understand how to do arguments passing and return values handling. As a side effect we are making our contract runtime Wasmer agnostic, e.g. we are not importing Wasmer dependencies.
* Cost estimation of contract invocation based on the contract size (see https://app.zenhub.com/workspaces/chainmiddleware-5cea2bcf78297c385cf0ec81/issues/nearprotocol/nearcore/2778);
* Nikolay I finished FAQ for contract runtime. Currently blocked by Max Z.

### Status update
* Nikolay I: Discussed with Willem how to split their current work items.
* Willem W: Pushed changes to aspect. Met with DevX team and brainstormed various projects we can set bounties for, e.g. add `include_bytes` into AssemblyScript. Started writing issues for the bounties and started looking into adding the fee.

### Note
* Evgeny K: Wasmer actually casts unsigned integers to signed behind the scenes, btw;
* Nikolay I: Wasmer thinking about providing interface to wrap other runtimes consistent with their interface.
* Alexey F: How about fusing Wasmer and Wasmtime and run both for each contract execution? Nikolay I: We might run it at a special mode. We might not even take a hit on TPS because it is very parallelizable.
* Willem W: Can we run V8 safely? Nikolay I: He might be the most familiar with V8, but it might not be easy to embed.
* Nikolay I: We should fuzz these machines. Max Z: We can repurpose fuzzers that some security audit companies provided for us.
* Bowen W: Bowen talked about contract migration with Dev X. The problem statement: user updates the contract code and they want to continue using the old state. Max Z: We might solve it by having rigorous best practices. Alexey F: We can constraint a bunch of stuff on near-sdk level. Evgeny K: We might do some work on near-sdk, e.g. we can include version of the contract into the key that we use to store the contract state. Nikolay I: Overall it might be hard to automate, because the translation is semantic in many cases.

## 29.05.2020

* Work group goals:
  * Coordinate the contract runtime work at NEAR
  * Less hard external dependencies, ownership of the contract runtime
  * Improve the runtime quality
  * More testing
  * Bugfixing
  * More specs
  * New runtime/compiler development
  * Collect runtime/compiler expertise at NEAR
  * Coordinate with the WASM blockchain community
  * Participate in WASM design processes, wherever relevant (i.e gas metering standards, GC proposal, etc)
  * Safety is an important priority

* Work group non-goals:
   * Define architecture of the blockchain from the runtime side, more reactive to the actual designs from the blockchain side
   * Be the only source of truth for runtime decisions at NEAR, more like advisory board

* Work group directions:
   * Maintain and improve Wasmer runtime
   * Understand the future contract runtime engine components, (i.e. stick with Wasmer, switch to something else, in-house development) 
   * Better understand AssemblyScript role at NEAR
   * Code analysis tools
   * Compilers for other languages
   * Near SDK?
   * Standardized testing environment for contracts
   * Best practises for contract devs via tooling
   * Verifiable reproducible builds
   * Verify VM correctness by running a node with an alternative VM implementation
   * Benchmarking for selected set of configs: interpreter, singlepass, cranelift, smth else

* Work group members:
   * Nikolay
   * Willam
   * Max as goals/priority provider from the blockchain side
   * Shall be generally open to other people as a 20% project, fluid per interest of people and the company
   * No runtime teams boundaries for sure
   * Can temp contract people on as needed basics

* Action items:
   * Agree on short/mid/long term goals
   * Use github https://github.com/orgs/near/teams/nearcore-middleware discussion group as a communication channel

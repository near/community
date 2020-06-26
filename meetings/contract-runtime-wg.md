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

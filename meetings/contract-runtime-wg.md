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

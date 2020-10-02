# EVM on NEAR
This thread contains agenda and/or summary of the regular EVM sync meeting. Please propose agenda items through the PRs and Issues.

Current eng team:
* Illia Polosukhin TL (running this meeting);
* Bo Yao. Eng, helping with fees, PR reviews, code audit;
* Mike Purvis -- trying to launch existing dApps (includes JS, examples);
* Misha Kever, Evgeny Kuzyakov -- final PR reviews (Evgeny as codeowner and Misha as an expert);
* Evgeny Kapun -- security-related reviews.

## 02.10.2020

### Status
* Not much update from Illia P an Mike P;
* Mike P added a simple express relay for Metamask. Mike P and Illia P to sync on multiple web3-providers :)
* Bo Yao: The fees are volatile, spent week debugging it. The EVM itself is not volatile -- at most 1% volatility. However, finalizing into blocks and writing state creates 30% volatility, which does not decrease when we increase the batch size. Also, note that regular state operations are actually not volatile, so this looks suspicious. Spent 2 days on investigating code. Max Z: Please create GH issues for next cycle and set estimates;
* Illia P: Needs to address comments form Eugene K on PR. Also coordinating with Bowen W to have EVM accounts ready on all networks, i.e. we need `evm` top account (we would have to erase an existing `evm` accoutn on Testnet during hardfork).
* Illia P: Added nonce to metatransactions. We need to register NEAR chain id in Ethereum repos, this is needed to prevent users from replaying the same transacions on multiple chains.
  * We might consider adding extra protection in metatransactions to prevetn replay, e.g. add block hash. To prevent the transaction from being delayed for more than some time. This might also be used instead of chain id, because it is safer;
* Bo Yao has investigated how to test contracts. EVM test suite is already testing Parity crates. Illia P: Web3 provider with truffle test suite already works, and we tested it with our partners.
 * Illia P: Suggests to add tests of some partners to nightly to run with truffle test suit.

### Notes
* 80% of Ethereum endpoints are implemented and others are not that frequently used. We were able to replace most of the stuff with similar semantics;
  * Max Z: We might need to add support of event subscription in the future, for which we might need to use indexer.
  * FYI We don't need proxy right now, it is good to have though. It is currently implemented through implementation of web3 provider.
* Alex Shevchenko: We need to put fees on metatransactions. E.g. charge custom ERC20 token inside NEAR EVM (e.g. bridged DAI) to be paid with, see dicussion in NEP about it. This should be done as a separate PR and assigned to Bo Yao.
* Another work item for Bo Yao: Make sure EVM is upgrade-friendly.

## 25.09.2020

### Status
* See checklist in the PR that needs to be done. Illia Polosukhin is still working on it;
* Mike Purvis is still working on web3js, connecting with partners, and relayer;
* Eugene Kuzyakov -- Reviewed EVM PR and found a bunch of critical bugs. Eugene has a major concern about introduction of remove subtrie operation on storage. When ETH contract suicides we need to remove all keys under it, which is similar to our account deletion. However for NEAR account deletion we use prefix iterator, which it cannot be used directly on EVM because of how Ethereum accounts are mapped to the trie. Deletion and iteration might be different implementations entirely. Also, we want to flatten the trie in the future and maybe replace merklelization which means iterate and delete operation might not be a good choice. Maybe we want to introduce sub-structure fro Ethereum accounts;
  * We cannot support operation to get block has for the given height. We have a hack for it. Evgeny Kapun: EVM only returns blocks up to some number, not up to genesis. This number is 256, which should be super easy to store for us even with GC enabled. It might require some changes in the chain. Unfortunately NEAR skips the block height, maybe we should return empty hash instead;
  * Eugene found a bunch of issues that allow passing data to precompiles and they would blow up the memory. However it is unclear whether EVM has extra protection against passign bad data, e.g. through pre-charging.
  
* Max Zavershysnkyi -- starting familiarizing with meta-transactions.

### Context

* Illia Polosukhin -- we are adding native support of metatransactions in NEAR EVM. (We can recover address of the user of ECDSA signature). In Ethereum smart contracts require extracting the address of the user (e.g. GSN contracts), while on NEAR we are going to have it natively. Our EVM will do the work of GSN, every contract inside EVM will see recovered address as `msg.sender`, unfortunately it requires end-contract to be modified.
  * Alex Shevchenko FYI. In NEAR metatransactions are not exactly needed, because we have allowance and predecessor_id. Evgeny Kapun: in NEAR or even on ETH we can have a smart contract that works as a wallet and proxies meta-transactions into regular operations. Unfortunately the UX flow is not clear.
* The way the flow works is that we are going to have a JS wrapper library on top of Web3js. Alex Shevchenko to write down detailed user flow, with interaction of components.
* The purpose of `msg.origin` in EVM -- is the signer. Currently we replace both `msg.sender` and `msg.origin` with recovered address for metatransactions, for non-metatransactions we use `predecessor_id` (maybe we can pass it as `signer_id` though).

## 18.09.2020

### Status
* Illia P was workign on adding ecrecovery. ecrecovery is critical for many existing Ethereum contracts. ecrecovery -- recover public key from signatures. Metatransaction -- user signs the message and relayer send it on behalf of the user. Go read https://github.com/nearprotocol/NEPs/pull/106 about metatransactions and mapping NEAR-EVM accounts;
* Bo Y finished EVM gas benchmarking, adding number to EVM fee config. Makes sure EVM gas does not include NEAR gas. Need to investigate why `funcall_cost_per_evm_gas` is so 20k times larger than ecrecovery cost.
* Mike P deployed a test netowrk (not to be confused with The Testnet) with EVM and some people are deploying and testing on it right now.

### TODOs
* Max Z, Evgeny Kuzyakov, and Evgeny Kapun to review PR;

## 11.09.2020

Let's define clear criteria for:
* Merging PR;
* Enabling on Betanet (blocked on param estimator and gas meter by Bo. Decide what to do with storage.); 
* Enabling on Testnet;
* Enabling on Mainnet.

It should be relatively safe to merge, because we don't have anyone deployed evm.

### Notes
* Bo approved the PR already. Ready for reviews by Evgeny Kuzyakov and Misha.
* Bo could not connect EVM gas counter correctly yet. Sometimes it fails because it is out of gas.
* Illia: Let's double check that entrance point is fine;
* Evgeny Kapun: Suggests to hardcode that every contract in .evm namespace only has EVMs;
* Illia: Storage is problematic, we need to make every function callt to EVM payable. Evgeny Kapun: What if we don't enforce storage staking on evm.
  * Illia: Things to consider: maybe we should consider how to improve state fees (should be introduce gas into storage?).
  * Let's make sure EVM-type storage is not abusable (receipt cost should prohibit abuse).

## 04.09.2020

### High-level info
* We are replacing EVM contract with native EVM;
* There are no hard deadlines but we need it ASAP.

### Milestones/Releases/Epics
#### 1. EVM precompile is ready to be merged (not ready to be launched) and can be tested on a separate network (requires fees connected);
* Integrate dApps from spreadsheet (assign to Mike P)
* Finish the spec;
* Implement the precompile
* Figure out nightly tests (assign to Bo)
* Fee estimation (assign to Bo) -- use real dApps for fee estimation and use them in nightly tests (will also create comparison to Ethereum)
* Write a blog post on how we integrate EVM

#### 2. Partners running inside EVM with all the tooling in “Testnet” (requires bridge support);
* Interop for EVM->NEAR interaction (will require help from Evgeny for runtime, near-sdk-rs)
* MintableFungibleToken in Solidity
* We need the bridge working with EVM, and we need connectors.

#### 3. Mainnet-ready
* Undecided yet

### Web3js status
Mike: Seems working, Suma says some routes might not be implemented. Logging needs cleanup. We might publish it now to npm.

### Notes
* Illia removed JSON serialization from EVM contract code;
* (orthogonal) Willem Wyndham is pulling out token library for Illia

### Discussion. Two interop ways for ETH->NEAR
* Specific hex(NEAR).bytes() precompile (suggested by Anton) -- will probably go with this one.
* Specific logs. Max Z: We cannot go with this one, because we cannot redefine gas cost for logs;


### Links:
* https://docs.google.com/spreadsheets/d/1kiIR3WtF5XsvfgdHILn48o8mglMN_GikCHuBIjcdEQ0/edit#gid=0
* https://github.com/ilblackdragon/balancer-near
* https://github.com/near/rainbow-token-connector/blob/master/bridge-token/src/lib.rs


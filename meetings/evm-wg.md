# EVM on NEAR
This thread contains agenda and/or summary of the regular EVM sync meeting. Please propose agenda items through the PRs and Issues.

Current eng team:
* Illia Polosukhin TL (running this meeting);
* Bo Yao. Eng, helping with fees, PR reviews, code audit;
* Mike Purvis -- trying to launch existing dApps (includes JS, examples);
* Misha Kever, Evgeny Kuzyakov -- final PR reviews (Evgeny as codeowner and Misha as an expert);
* Evgeny Kapun -- security-related reviews.

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
https://docs.google.com/spreadsheets/d/1kiIR3WtF5XsvfgdHILn48o8mglMN_GikCHuBIjcdEQ0/edit#gid=0
https://github.com/ilblackdragon/balancer-near
https://github.com/near/rainbow-token-connector/blob/master/bridge-token/src/lib.rs


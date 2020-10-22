# Ethereum-Near Bridge
This thread contains agenda and/or summary of the regular Bridge sync meeting. Please propose agenda items through the PRs and Issues.

## High-level Goals
We pursue safety and usability at the same time.
* Current focus:
  * Safety. We close all findings related to the audit, see: `2020-07 audit` label: https://github.com/near/rainbow-bridge/issues?q=is%3Aopen+is%3Aissue+label%3A%222020-07+audit%22
  * Usability. [We have already split the repo](https://github.com/near/rainbow-bridge/pull/319). [Now we extract RainbowLib](https://github.com/near/rainbow-bridge/issues/297) Alex K discuss RaibowLib usages with Illia and Chad;
* Next focus:
  * Safety. We address the remaining issues in the `Fully Tested Bridge` release in order of their priorities, see https://app.zenhub.com/workspaces/chainmiddleware-5cea2bcf78297c385cf0ec81/reports/release?release=5ed9658f026fd74dde14faec
* Usability. We work on documentation and examples.

## Action point long list
- [ ] ???

## 22.10.2020

**Alex Kouprin**
- Working on architecture for isolating components, enabling testing and fixing dependencies

**Vlad Frolov**
- Working on redesign & naming (together with Alex Kouprin)
- Current focus is the near/rainbow-bridge#344 approx. 20%

**Evgeny Kapun**
- Proposal of architecture change (transmission of the events): *link*
- Security issue found for token connector, though it's the issue of the events transmission
- Architecture change can lead to significant reduction of the Ethereum side gas costs for client;

**Marcelo Fornet**
- Found an issue with generalized token connector that can be exploited where a malicious actor can submit an incorrect proof. Currently fixing it;
- Working on specific tests following discussion with Alex Kouprin;
  - Checking proofs from Ethereum on NEAR side, which also includes checking events deserialization. It turned out to be easier to cover all corner cases of Eth event verification;

**Chad Ostrowski**
- Will work on support generic token connector on the front-end app. 
- Added support of native ERC20 token;
- There is some weirdness when NEP21 is sent back as ERC20 it is not getting unwrapped as ERC20;
  - AI: Alex Kouprin and Chad Ostrowski to investigate. Eugene Kapun thinks it is not a design flaw, we should solve it on the front-end side to check if NEP21 is already matched to ERC20 then we don't need to wrap it additionally.
- After Chad is done adding support of token transfer in 4 directions, will make a library out it.

## 15.10.2020

### Status
* Alex Kouprin shared a plan for bridge structure, see: https://docs.google.com/document/d/1b1MH0WSR3eHLow7iJ4qlcDJy207Kgrfdls7ZG3AB2ak/edit
  * Alex Shevchenko: Supposed users want to use multiple token connectors, does it mean they will use multiple CLIs? Does the CLI tool need to know about all connectors that will be released? Supposed we have 3 types of connectors: native tokens, fungible tokens, and non-fungible tokens. Alex Kouprin: we will include common token connectors into the library. Chad: We might want to have some pluggable architecture. Vlad Frolov: Let's start with the current plan and adjust later. Max Z: How to test bridge without token connector? Vlad Frolov: Evgeny Kapun said we can use low-level API to check that we can pass headers and verify proofs.
* Max Z: Please plan today and tomorrow what you will be working on in the next two weeks.
  * Vlad F: Started working on https://github.com/near/rainbow-bridge-lib/issues/16 but did not have a chance to close any issues yet;
  * Evgeny Kapun: Closed https://github.com/near/rainbow-bridge-sol/issues/23. There is optimization PR https://github.com/near/rainbow-bridge-sol/pull/24 that is ready to be merged. Will work on improving tests for Solidity contracts, and they need refactoring. Then will work on further optimization of Solidity. Make sure to communicate with Marcelo Fornet when you start looking into Rust contracts. Alex Kouprin: Needs help to review currently open PRs, and decide if we close them or open them. Many findings of the audit are already outdated because of Evgeny's optimizations. Alex Koupring: Let's sync with Evgeny Kapun;
  * Marcelo Fornet: Still working on https://github.com/near/rainbow-bridge-rs/issues/10 turned out to be much more complex to learn about all corner cases of RLP, event serialization, and MPT. Alex Kouprin: Asked Marcelo to synced with Bo Yao, since Bo Yao already worked a lot on RLP encoding and such.
* Alex Shevchenko -- had calls with partners, will continue collecting feedback with partners. Will address planning issues and create milestones for the next 2 months. It is a bit waterfal-ly :) because we have hard expectations by business partners. Would have to sync with Alex Kouprin and the team on when we are going to deliver.
  
### Notes
* Please take over Rinkeby bridge, please make sure documentation in main repo is up to date. Alex Kouprin: Let's have a sync to do transition.
* Hackathon: Alex Kouprin please reach out to Nima and discuss the hackathon evaluation timeline.

## 08.10.2020

### Status
* Evgeny Kapun -- found a problem with test with downloaded proofs, i.e. proof dump. However, it is not a good approach, because they don't guarantee to cover all corner cases, e.g. with max num of validators, tests with different lengths of merkle paths. Evgeny Kapun suggests to keep prover simple, so that we can remove block hashes. We need to decide what to do with: https://github.com/near/rainbow-bridge-sol/pull/25 Discuss async;
* Marcelo Fornet -- trying to figure out how Ethereum logs work to check for all corner cases;
* Vlad Frolov -- started with onboarding with CLI and rainbow LIB. Had dicussion with Alex Kouprin. Alex Kouprin has a plan how to split CLI in two entities: user facing and CLI for testing;
* Alex Kouprin -- collecting insights of what happened in the past several weeks. Alex's current focus: let's finalize high-level view of the architecture, and there are still some issues;
* Chad Ostrowski and Alex Shevchenko also came up with a high-level design of the bridge;
* Chad Ostrowski -- partner needs a fiat onramp by January, they might need to only send DAI across. Moved front-end to near organization and Vlad Grichina helped with some Borsh handling, and use it from near-api-js. Published a new 1.1.0 of the UI that uses new contract, so people lost old tokens;
* Mario Halambek is blocked on upgrading nearup for bridge, and then will be working on monitoring;
* Alex Shevchenko -- created a doc with user needs. We need to pay attention to ability NEAR NEP21 with nERC20 running in NEAR EVM. Will be consulting with partners to get feedback on it. Will work with everyone here to come up with the roadmap that we can communicate to the outsiders;

### Notes
* Hakacthon participants are still not evaluated, and we need to keep bridge running for them;
* Evgeny Kapun -- the upgadability is slowed down by difficulty developing on our repos;

## 01.10.2020

### Status
* Onboarding Marcelo and Vlad to work on the bridge;
* Two severe incidents: https://nearprotocol.pagerduty.com/incidents/PYCB5WQ and https://nearprotocol.pagerduty.com/incidents/PRKD6UO. Maybe should've been SEV-1;

### Focus
* Safety
  * Evgeny Kapun still haven't merged https://github.com/near/rainbow-bridge-sol/pull/20, stuck on publishing the packages. CI tests are actually passing, but the way our CI is setup it causes the CI to fail. Ideally the repositories should not having interfering CI;
* Usability
  * Still blocked by not know exact usages for all roles. Alex Shevchenko discussed it with Chad and Evgeny Kapun. Alex will draft a document on roles and user flows to help us clarify the interfaces and the repo split; 

## 24.09.2020

### High-level goals
#### Safety
* Follow Alex Kouprin's issues: https://github.com/near/rainbow-bridge-cli/issues/338 and https://github.com/near/rainbow-bridge-cli/issues/339
* Evgeny Kapun, Marcelo Fornet and Max Zavershynskyi working on it.
  * Extract near-api-js kludges and submit them to near-api-js repo if they are actually still needed. Requires Marcelo to be able to start bridge on Rinkeby and NEAR Testnet.  Also see https://app.zenhub.com/workspaces/mainnet-guild-5cea2bcf78297c385cf0ec81/issues/near/rainbow-bridge-cli/307
  * Then start working on P1 issues from "Fully Tested Bridge" release (together with Max)
  * Evgeny Kapun still working on merging rewrite of NearOnEthClient. CI fails, but local run passes. There is a serious problems with near-api-js that does not allow distinguishing timeouts and account does not exist. Web3js just slightly easier to use with errors. Relevant: https://app.zenhub.com/workspace/o/nearprotocol/nearcore/issues/2536 . Need help from Bo Yao to debug CI issue;
  * Evgeny will optimize serialization (there is already a PR that needs to be merged). Evgeny is currently writing a design doc on improving the bridge, not just about not storing excessive storage. Currently API is not compatible with upgradability. Token connectors include code that parses events, which makes it non-upgradable: https://github.com/near/rainbow-bridge-sol/issues/19

#### Usability
* Alex Shevchenko needs to revisit https://github.com/near/rainbow-bridge-cli/issues/313 and figure out the exact interface of Rainbow Lib and user the stories behind this. Definitely should work with Chad on it. (Generic connector is a separate library). Relevant: https://github.com/near/rainbow-bridge-lib/pull/12

#### Hackathon Status
* Chad's next work would update the current example to generic token connector. Marketing also wants to have a landing page for the bridge. These two might actually be integrated. Maybe someone else should be implementing the landing page, talk to Jake about design and implementiation.
* Technical support -- seems like we are keeping up with questions.

## 17.09.2020

## Status
* Max Z: Started bridge on Rinkeby (enabled by Bo allowing trustful mode);
* Chad setup ERC20 that anyone can mint (TODO: Add to CLI documentation);
* Evgeny Kapun found 3 critical issues with Solidity contracts post security audit (having trouble with testing):
  * AI for Max (or Alex Kouprin) -- add very specific step by step documentation on how to make changes to any repo, test, and publish it (Bo updated documentatino in https://github.com/near/rainbow-bridge-cli/pull/335 which is blocked by https://github.com/near/rainbow-bridge-sol/pull/11). FYI can ask Bo questions about testing;
* Illia sent a proposal for Rust upgradability see https://github.com/near/rainbow-bridge-cli/issues/298 ;

## Focus next 2 weeks:
* By Evgeny Kapun
   * upgadability for Solidity;
   * JS tooling;
 
## Notes
* Chad: No good documentation for deploying generic token locker;
* Mario updated bridge to use fresh nearup (we will be using old nearup until hackathon is done);

## Problems
* Security of JS code -- passing secret keys is not security;
* Need to separate testing (should live entirely in npm test) and production commands (rainbow) (separate Ganache and nearup);
* Better UI, usability, separation of UIs (many things might be solved through good documentation):
  * Relayer role (should be able to run `rainbow up` on the given config);
  * Developer role (ideally for development they don't even need CLI);

## 10.09.2020

## Hackathon Status
* Rinkeby testnet is going to be available, but we need to fix P0 bug: https://github.com/near/rainbow-bridge-sol/pull/16 (Bo can help);
* Generalized token locker. Alex K started integrating it today and need some help to complete integration of it;
* Chad needs to make demo app work for specialized locker, and Chad is getting stuck on various pieces. Alex K and Willem are helping him;
* Make sure documentation is polished -- Max Z. Documentation of the dApp is on Chad;

## Notes
* Daniela O says Rinkeby might be forky. Gotta be careful. Maybe we should launch our own testnet? Daniela can check if we can have PoA testnet running for it.
* On Monday we might deprecate for nearup. The new nearup is a branch. We might merge it after the hackathon;

## Post-hackathon goals
* Let's focus on hackathon for now, after that let's focus on stability, e.g. testing infra, similarly to what we use for nearcore. Post hackathon we can also use the feedback to guide us further.
* Mario is working on alerting system. Will just pipe block status of the relay. Will also somehow get number of transactions from the relay. Might make rainbow status service for exporting metrics.
* Nick might be working on setting up Ethereum Testnet SRE -- can be just one node, but gotta be PoA.

Please look at:
https://github.com/near/rainbow-bridge-cli/issues/339

https://github.com/near/rainbow-bridge-cli/issues/338


## 03.09.2020

## Hackathon Status
* Had a brainstorm on what to tell people to build on hackathon;
* Might bundle EVM into hackathon;
* Max still haven't launched it on Rinkeby, specifically ETH->NEAR direction. Bo will take over https://github.com/near/rainbow-bridge-cli/issues/336 ;

## Stability and Robustness Status
* Most work will be done by Alex K;
* Evgeny K contracts -- audit (current focus), adressing Solidity splinters, uprgadability.
  * Found an issue in prover code.
* Mario need to talk to Alex K. Repurpose `status` command to setup https://github.com/near/rainbow-bridge-cli#deploying-and-using-locally ;

## Notes
* Chad. Local setup might be tricky, specifically Wallet and Contract Helper;

## 27.08.2020

## Status
* Max Z: Start looking into upgradability;
* Bo Y: Separated Rust contracts and fixed some issues with packaging ethhash-proof so that we do not on github anymore. Fixed CI for all repos, rainbow-bridge-sol and rainbow-bridge-rs repos will trigger CI on the rainbow-bridge-cli repo. Resuming transfer PR had to be split into repos;
* Alex K: Great job splitting repos really fast! RainbowLib is now separated too into a separate repo. The next steps are to get feedback from Illia and Chad to get better usability understanding. Created some small issues for all repos. Bo Y helped to fix cyclic dependencies, so we can test modules separately, allowing us to have better unit tests. In general, Alex K wants to figure out how to increase test coverage, including audit issues;
* Illia P: Made generalized token locker with isolated tests for Rust side, which allows to mock the prover and test the connector side. The next steps are to introduce it into testing into CI. Need help from Bo Y or Alex K;
* Chad O: Demo on sending ERC20 to NEAR. Will be blocked soon by not having addresses of contracts deployed to Ropsten and Testnet. <- Max needs to unblock by giving addresses, needs work Bo Y;
* Ash C: Figuring out how many partners need bridge and what they need. Making sure fits everyone's expectations;
* Evgeny K: Reading Solidity contracts. Find some undeeded parts, e.g. ECDSA code. Will move ED25519 generator into repo to generated it during build. Might turn into library, even though there are issues with deployment. Max Z: Please see https://github.com/near/rainbow-bridge-cli/issues/214 ;

## 06.08.2020

### Current state
* Security audit reports are in;
* We need bridge on Mainnet in 2 weeks with upgradability;
  * Max Z have discussed upgradability design with Anton Bukov. The biggest difficulty would be the e2e tests;
* P1 bugs are mostly closed, thanks to Bo Y;
* Alex Kouprin is onboarding, will start with implementation `node index.js status` command;
* Max Z will discuss testing of the bridge with Alex S and Bowen W;

### Priorities
1. Bridge on Mainnet with upgradability within 2 weeks from now;
2. e2e tests for watchdog (already a PR by Bo Y), upgradability, and maybe fuzz testing proposed by Alex K;
3. Dedicated node setup for the bridge -- Sandi will take care of it after next week;
4. We need to close/address all security audit findings, including their comments about lack of testing, code quality and code style (renaming) -- Bo Y will start working on it (hopefully will address them next week), Alex K will join as soon as possible;
5. Monitoring for the bridge using indexer and Graphana -- by Mario.

## Action items (in reducing priority)
* Max Z -- implement upgradability, e2e test for upgradability;
* Bo Y, Alex K -- remaining P1 issues, security audit, `node index.js status` command, other e2e tests;
* Create issue for Sandi -- setup full archival nodes behind VPC with bridge;
* Create issue for Mario -- for monitoring infrastructure for bridge (assign Sandi, Mario);

## 30.07.2020

### Current state
* Audit is done, but they are writing the report;
* Still figuring out the deadlines with marketing;
* People working on the bridge: Bo Y, (1-2 days a week Max Z), Alex K (hopefully will start next week);
* Good news: it seems to be running more or less stable on Mainnet testnet <> Ropsten (the only issues:  https://github.com/near/rainbow-bridge/issues/184 and https://github.com/near/rainbow-bridge/issues/232 ), the critical double-spending vulnerability is fixed by Bo Y, the other two important issue are (https://github.com/near/rainbow-bridge/issues/208 and https://github.com/near/rainbow-bridge/issues/207 where 207 has already a PR);

## Action items (in reducing priority)
* Max Z -- implement upgradability, e2e test for upgradability;
* Bo Y -- P1 issues (especially 232 and 184), also setup e2e test for challenges;
* Create issue for Sandi -- setup full archival nodes behind VPC with bridge;
* Monitoring service decoupled from relays and setup prometheus for it. Create an issue and work item for Sandi and Mario (also add SRE label);l

## 23.07.2020

### Current state
* Audit ends this week, then 1 week of mitigations;
* Large backlog of work items;
* Max is trying to restart Ropsten testnet. Difficulties are: Ropsten is slow and gas price is going up. Rinkeby testnet?


### Things to do
* Max to implement upgradability;
* Someone to implement incentives.

### Notes
* Add more testing to bridge e2e.

## 16.07.2020

### Current state
* Going through security audit (1 week left);

In the order of importance:

* P0 Bugs:
  * bps in initialization -- Bo Yao;
  * bps length not matching -- Bo Yao -- it only works if set of block producers do not change, because you do verification first and then set the block. For the first block in new epoch we currently verify using past past validators. Bo Yao to fix;
P1:
* nearcore CI test for bridge -- already directly added to buildkite;
* Robustness -- in much better shape thanks to Bo Yao;
* Upgradability -- Max not started on it yet;
* Tests coverage -- someone (depending on Bo's availability);
* Documentation -- Max hasn't started working on;
P2:
* ethash rewrite;
* We need reproducible nearcore light client RPC for gas fee optimization of Solidity contracts.
* We can start Solidity borsh into borsh-sol library, also borsh-js needs unification;

Sandi Fatic's availability is low, should be better after next week.

## 09.07.2020

### Current state
* Going through security audit (which will end in 3 weeks). They have frozen the commit;
* Things that are missing:
P1:
  * Testing -- Alexey and Bo;
  * Robustness -- Bo. Replacing eth-objects and some other JS libraries is in this scope;
  * Upgradability -- Max;
  * Documenation -- Max;
  * More use cases -- Max, Alexey?, Bo?. Evgeny Kuzyakov could help with NEAR contracts. Matt Lockyer can help out with Solidity contracts;
  * Sandi can help with spillover work items that are urgent, but he is needs documentation;
P2
  * ethashprover rewrite;

### Status report
* Bo Yao -- few PRs to improve e2e test. One PR to dump and verify nearprovers. Small PR to rename CLI and publish to npm. Moved pipeline definitions into the repo. Managed to workaround issue with ganache that we don't need Node 10 anymore. Added retrier to the contract wrapper. Spent a lot of time comparing differences of Web3 responses and RPC responses, we need to adapt the encoding of Web3 format. Our external dependencies do not do correct rlp encoding of the header;
* Alexey Frolov -- working on the testing of the mintable token. Testing mint and burn by mocking the prover. Using standalone runtime for testing;
* Max Zavershynskyi -- fixed some critical bugs, writing documentation, lint cleaning.

### Notes
* Bowen Wang: When is upgradability? We will change blockheaderinnerlight within a month. Max Zavershynskyi was planning to implement it within next 2 weeks;
* Max Z, Evgeny K: Let's release stable version of standalone runtime. We might not be able to publish it yet.

## 26.06.2020

Proposed agenda:
 - Speeding up NEAR -> ETH path
 - Tx pricing to cover fees on both side
 
## 26.06.2020

### Current state
* Security audit begins on July 6th, will take 3 physical weeks;
* Only have ERC20 from Eth transferred to Near and back;
* We now have a bunch of tests, kudos to Alexey Frolov and Bo Yao;
* Max Zavershynskyi still connecting some code for Near->Eth transfer (the only missing thing which is executor_id in nearcore was added by Bowen Wang);
* P0. Bo Yao is investigating ed signature failing on new blocks from testnet. Interestingly the old blocks are passing (those that were commited to master) but new blocks are not. Bowen Wang could help to debug if we can reproduce it. Bo Yao has a script that Bowen Wang can run to reproduce it;

### Missing components
* transfer-erc20-from-near-to-eth cli command -- Max Zavershynskyi;
* Watchdog service that will monitor the signature and challenge them if needed -- Max Zavershynskyi;
* Alexey Frolov is adding more integration tests for Rust contracts;
* Documentation to help security audit.

### Small broken things
* Fix cleanup in the e2e test on CI. Sorry Sandi Fatic that Max reverted it;
* Introducing ganache-core back by Sandi Fatic;
* Freeze all dependencies (fork github dependencies, specify exact versions in package.json, etc);
* Sandi Fatic will do npm publishing next week;
* Sandi Fatic cli does not tell which parameter was not specified.

### Notes
* Illia Polosukhin -- we added upgradability to nearcore, but it should not affect light client;

### Action item
* Make our logs general byte blobs, right now they have to be UTF8 strings. Nearcore -- Evgeny Kuzyakov.


## 19.06.2020

### High-level focus
* Getting contracts ready for the audit;
* Keeping it usable for our partners;

### Status report
* Max Z: environment/cli -- adding commands. Haven't added the command to do Near->Eth transfer yet. General cleanup and refactoring of the repo, will focus more on the documentation next week;
* Bo Y: making cross-contract tests for bridge-prover. Upgraded near-sdk-rs with help from Evgeny K. The tests are close to finish, there is an issue with standalone runtime returning empty response for the cross contract call. Bowen W: You might need to explicitly wait for this transaction to finish computation of all receipts. Ask Evgeny K for example of waiting for transaction execution. Earlier this week fixed the CI, made linter allow-to-fail;
* Sandi F: Finished dockerization. Figured out why ganache-core was not compiling. Fixed nearup setup. Fixed the prepare with no arguments. Will work on nearcore until Max Z files more issues;
* Alexey F: Created PR with clippy fixes, and overflow checks. Later will work on unit testing of the contract internals, might add fuzz testing for the Eth2NearProver;
* Anton B: Checked changes by Bo Y and Max Z. Will look through smart contracts to give it another pass of refactoring.

## 12.06.2020

### Current focus
* Fixing Near2EthProver -- Anton B needs help with from Bowen W. The code is pushed into master, you can run `yarn test` to see test that fails on verification of the proof. Bowen W kindly agreed to help, and Max Z will also look into it;
* DevOps:
  * Bo Y -- wrote eth header dump, and added Rust test for CI that checks it. Added prover tests too. Then added other test for CI. Max Z: suggestion to focus on client and prover, rather than fun token because it was already security audit reviewed.
  * Sandi -- implemented start/stop commands. Started working on the config that Max Z took over. Currently working on Dockerization. Dockerized ganache and near node (but nearup sometimes fails). It is not clear how to pass config.json file to eth-relay, maybe docker image needs to grab it on build.
  * Max Z -- finishing the config, will also look into prover issue, will add near-relay and init-eth-contracts.
  
Let's aim for finishing prover, devops, and docs until next EOW and pass it to security audit companies.

## 05.06.2020

### Status Update
* **Verifier of transaction outcomes on Solidity -- Anton B** -- almost completed, will dedicate few hours over the weekend to finish it up. Asked Bowen W to provide example of the proof, there are some missing items in the arrays of the proof. Bowen W: It is empty because we have one shard. Anton B: Currently skipping deserialization of this;
* **ETH->Near transfer of ERC20 on mainnet -- Max Z** -- done. Found a bug in prover;
* **https://github.com/nearprotocol/nearcore/issues/2712 -- Bowen W** -- nearcore is implemented, but near-api-js part is missing;
* **There are actually some bugs in our ED25519 implementation according Evgeny Kapun. E.g. Anton B found a bug where zeroed pks always pass verification.** -- bugs fixed by Evgeny K and Anton B has updated the code. Some new problems -- this made our contract deployment more than 10M gas. So we need to deploy this library separately. It uses different linking on Solidity in contrast to regular contract call. It might produce some problems in the future. Maybe we can make it a standalone smart contract;
* **Bowen W and Anton B to discuss offline the header hash mismatch.** -- was resolved. Spec needs to be fixed, not sure if Alex S has merged it yet.

DevOps:
* Sandi -- replacing Python with JS. Implemented the start comment, addressed Bo's and Max's comments. Relay fails on "contract is already initialized". Adding `ethbridge0` to the code broke it. Got everything running with pm2 -- proper process management, nearup, ganache, relay all run separately. Next thing is to split the start command. Also need to expose hardcoded constants into args;
* Bo -- Implemented block dumping and using these blocks in Rust tests. Max Z: Let's dump these blocks to S3 and then run tests against them on CI. Next step: dump proofs and feed it to the prover;


### Action items
* Make sure bridge works with many shards.
* Make sure the fix to the lightclient spec is merged.
* Anton B: Need to call ED as a separate contract rather than a linked library.

### Notes
* Anton B: See feedback about cross contract API, specifically about separatign bindgen-level stuff;
* It is okay for Solidity contract to record used events forever. It is a common pattern, e.g. some order books record indefinitely such information.

## 29.05.2020

### Summary
Things to do:
* Verifier of transaction outcomes on Solidity -- Anton B;
* ETH->Near transfer of ERC20 on mainnet -- Max Z;
* https://github.com/nearprotocol/nearcore/issues/2712 -- Bowen W;
* There are actually some bugs in our ED25519 implementation according Evgeny Kapun. E.g. Anton B found a bug where zeroed pks always pass verification;
* Bowen W and Anton B to discuss offline the header hash mismatch.

DevOps:
* Sandi Replacing Python code with JS. Revisiting design: we will use pm2 and docker compose to launch services. Bo Y: We should not actually use docker compose, see discussion here: https://github.com/near/rainbow-bridge/issues/58 . Sandi already sent a PR with refactoring and moving some logic. Next week it should be done;
* Bo Y: CI tests that excersize Eth2NearClient. Bo Y figured out how to do it, but haven't actually implemented it yet.

### Missing components
* **Solidity contract locker (Anton B to implement and give to Max Z) -- Anton B.** DONE for ETH->NEAR transfer;
* **LightClientBlockView verification in Near2EthClient -- Anton B.** Just done by Anton B. There was a bug in light client spec. There are still problems with signature verification, valid signatures are returned as invalid. Need help Evgeny Kapun and maybe Alex S or Bowen W, see below.
  * **Validate ED25519 using two blocks from two sequential epochs from Near RPC -- Anton B**. Not done yet. Bowen W can help find the exact code that constructs our signatures;
* **Add prev epoch headers into merkle tree of LightClientBlockView -- Bowen W (https://github.com/nearprotocol/nearcore/issues/2711)**. Done!
* **Create a script that does end-to-end transfer of a token from Eth to Near -- Max Z**. The janky script is working, need to make it look more usable and plan to do transfer from ETH mainnenet to Near testnet. Bowen W to give Max Z some testnet moneyz;
* **Fix Near2EthClient following the updated spec -- Anton B (ETA 1-2 days, need to use this branch now https://github.com/nearprotocol/nearcore/pull/2686)** DONE!;
* **Near2EthProver following the spec -- Anton B (ETA 1-2 days, assuming there is test data)**. Need explanation on how to verify transaction outcome. Need to get some test data. Bowen W: wrote an e2e test that extracts and verifies proof for transaction outcome. There is a Python code that can be followed to implement Solidity. Bowen W: to send code pointers;
* **Implement proof extractor as an RPC endpoint -- Bowen W (RPC part is done here: https://github.com/nearprotocol/nearcore/issues/2712)**. Implementation is done, needs Alex S and Vlad F reviews. It is a 1000 LOC change;

### Notes
* Anton B: unclear whether the height should be consecutive. Bowen W: The height must be consecutive;

## 22.05.2020
### Discussion of contract lockers
* Anton B to write contract locker in Solidity example for Max Z.


### Status Report
* Anton B is blocked on https://github.com/nearprotocol/nearcore/pull/2686
* Anton B and Evgeny Kapun had to update ED25519 implementation to accept 41 bytes. Anton B also verified that input to ED25519 is fed correctly;
* Anton B had to convert JSON coming from Near RPC to JSON;


### Notes
* Max Z: we need to check everything but the signature verification upon accepting block into NearClient;

### Missing components
* Solidity contract locker (Anton B to implement and give to Max Z) -- Anton B;
* LightClientBlockView verification (without signatures) in Near2EthClient -- Anton B;
* Validate ED25519 using two blocks from two sequential epochs from Near RPC -- Anton B;
* Add prev epoch headers into merkle tree of LightClientBlockView -- Bowen W (https://github.com/nearprotocol/nearcore/issues/2711);
* Create a script that does end-to-end transfer of a token from Eth to Near -- Max Z;
* Fix Near2EthClient following the updated spec -- Anton B (ETA 1-2 days, need to use this branch now https://github.com/nearprotocol/nearcore/pull/2686);
* Near2EthProver following the spec -- Anton B (ETA 1-2 days, assuming there is test data);
* Implement proof extractor as an RPC endpoint -- Bowen W (RPC part is done here: https://github.com/nearprotocol/nearcore/issues/2712).

### Nit
* Rename EthClient methods to block_hash_unsafe and block_hash.

## 15.05.2020

* Anton B: Two P0 tasks -- bonds/deposits of the relayers.  Maybe we can change bonds because right now the delay is one hour. Max Z: suggests to go with the simplest design possible. Anton B: making it work in such way that multiple headers can be submitted in a row is easy, but let's keep the simple one for now. We have two constants now: lock duration and one lock eth ammount. Second P0 task -- implement verification of elliptic curves that do not exist in Solidity ATM, ED25519. There were a lot of parts missing for signatute verification. Needs SHA512 is used internally for ED25519 verification. Anton B already knows where to get SHA512 implementation, see smartpool implementation.

Anton B: there is a bug that implementation does not borshify. Bowen W: Unfortunately the change is the protocol level change. TODO: File issue for adding borsh.

* Max Z: Alex S found bugs in proof extractor and Eth2NearProver. It seems like these are all bugs, but we need to polish some code around nibbles and paths in extension/leaf. Going to be working on tests and CI;
* Bo Y: productionized some devops, e.g. added docker.

### Notes

* Anton B and Max Z: Noticed some strange behaviour when trying to get `LightClientBlockView` for block indices far from the head and close to genesis. TODO: File issue;
* Anton B need to pair program ED255129 and SHA512 verification in Solidity see https://github.com/near/rainbow-bridge/issues/42 ;

## 08.05.2020

### High-level goals
* Anton B: NearClient testing -- we need to extract light client borsh objects and write tests with them;
* Anton B: NearProver -- next step;
* Missing components from the DevOps:
  * Productionize it;
  * Connect NearClient?
  * There is a Web3 bug.
* EthProver -- fixing on Max Z;


### Progress
* (copied from Middleware Sync) Anton B: Switched from EthProver. EthProver has some issues with validating nodes of the trie. Switched to NearClient, read already the spec of the Light Client. Updated all the structures that are Borsh deserialized. Partially implemented light client verification;

AntonB: Updated the code to the new light client spec, following the spec. Rewrote some deserialization code. Added ability to calculate hash of raw serialized bytes. Wrote light client verification code, but we cannot test yet.

## 01.05.2020

### Progress
* Pair programming with Misha. verify_trie_proof seems to be fixed. However it seems like not paths in that function are getting executed. Anton B is working on created more tests that excersize all paths.
* Currently using index.js to print stuff to plug it in later into tests.rs;

### Action items

* Brush up the rainbowup Zenhub board
* Extract various proofs from Mainnet and submit it to the EthProver to have proper tasting. The file called extract.js
* Rework index.js into a testing service and use rainbowup.

After this is done:
* Anton B will continue working on NearClient. It is all about implementing the client spec (The borsh decoding should be working already);
* For NearProver we need to decide what exactly we want to prove.

* Should also rename ethbridge and nearbridge to *client;

### Useful links for EthProver
* https://github.com/ethereum/wiki/wiki/Patricia-Tree#example-trie
* https://ethereum.stackexchange.com/questions/268/ethereum-block-architecture/6413#6413



## 24.04.2020

Since Middelware Sync:

* Anton B: Worked on slides for DeFi primitives demo. He tried to have a focus on DeFi aggregation. Currently working on: extracting proof, and deserializing ethereum structures in Near contract (some issues on this line: https://github.com/nearprotocol/near-bridge/blob/ethprover-test/ethprover/src/lib.rs#L113 . There are two different types of receipt serialization, one before Byzantine fork and one after). Some difficulties with reproducibility of commands on Ganache. After working on deserialization issues will work on proof issues;
* Max Z: Rainbowup can do prepare, run, stop, clear for Near node and Ganache. Attaching relayers.

### Notes
* If there is a security issue in one of our libraries and we fix it we need to notify everyone who depends on our libraries.

## 17.04.2020
* Eth event prover -- Anton B: some progress on DevOps, got stuck, needs help from Evgeny K. Has problems when bash script is composed of other bash scripts, the traps are not working when they are defined in multiple bash scripts.

### Notes
* Add nearup to npm;
* FYI Max Z is working on bridgeup;
* Anton B: Wrote script that extracts events from Ganache. Will extract proofs and use them for testing so that we don't need to relaunch. This will allow us to parallelize work on Eth prover fixes and DevOps;

## 10.04.2020
* Eth event prover -- beginning of the next week;
* Near result prover -- not implemented yet. (Important PR with fields reordering was merged by Alexey F);
* Near light client -- waiting for the new spec from Alex S. (Depending on the spec will take from several days to several weeks);
* Token example -- will reuse components from Eth event prover testing.
* Cross-contract calls potentially built by community. 

### Notes
* Anton B: In the future, we will introduce additional smart contracts for IBC compatibility on Eth bridge;
* Anton B: Discovered OpenZeppelin tool, OZ, for Ethereum contract compilation, deployment, etc. Will have with DevOps and will replace current bash scripts. Would need to loop in Bo Y or/and Vlad F to help with DevOps;
* Anton B: Getting close to write test that certain Eth event was included in the block;
* Anton B: Refactoring Bash scripts;
* Anton B: Cross-contract calls might require additional security, like checking whether the call is performed through callback.

## 27.03.2020
* Verifiers QA - March 25 - 29;
* Near light client - plus 2-3 weeks. Still reading and researching spec;
* Token example. April 5 - April 12;
* Cross-contract calls.

### Notes
* Some progress with calling verifiers from other contracts. A couple of more days needed to wrap up;
* Just made script to work with local node;
* Max Z: Ask Alex S to provide formal verification rules;
* Anton B: Instantiation id for the contract deployment. And then pass additionaly instantiation id with account id, as predecessor instantiation id. This would be useful for sharded contract calls mental model that we can build the course around. Can be added past Mainnet;
* Anton B: Still considering multicontract per account, post Mainnet. Try to add some code to make it forward-compatible;
* Anton B: Important thing to take care of: Make sure our contracts are not breaking when we change fees. Interesting idea: if we really need to increase the cost of one operation we need to lower everything else to amortize the total cost;
* Anton B: We should consider having a write-up on rules an guarantees like: we will not increase fees, etc;
* Anton B: Consider being able to run old contracts with old runtimes. TON blockchain had an idea of TON runtime;
* Anton B: An article on how blockchain contracts should not be used: https://medium.com/bitclave/how-to-cook-blockchain-right-dedaed1da57d

## 20.03.2020
Pushing deadlines by one more week.
* Verifiers QA - March 25-29;
* Near light client - plus 2-3 weeks;
* Token example. March 29- April 5;
* Cross-contract calls.

### Notes
* Soliciting ideas for the online hackathon;
* We use Ethash proof software provided by kyber to compute DAG proofs. The tool operates with 32-bit manipulations, maing it hard to understand.
* We have different source of pseudorandomness for header verification: https://github.com/ethereum/go-ethereum/blob/525116dbff916825463931361f75e75e955c12e2/core/blockchain.go#L863 It should be fine if we just ignore it.
* We need to describe in detail what we want to implement on the top of the bridge;
* gsn.openzeppelin.com --  nice documentation on interacting with the contract (skip part of docs related to approvals);
* There is Solidity contract with Borsh library. Trustful contract already has elements of trustless contract;
* Illia P, Anton B: In the far future, consider building sharded DeX on near that interacts with its users in p2p manner.

### Action items
* Evgeny K to send token standard NEP to Anton B;
* Evgeny K and Anton B discuss sharded contract standard.

## 13.03.2020

### Agenda
Pushing deadlines by one week.
* Verifiers QA - March 18-22;
* Near light client - plus 2-3 weeks, need to check the lightclient spec again;
* Token example - requires contracts for both sides and nodejs scripts. March 22-27;
* Cross-contract calls - will try to engage community. Maybe run a hackathon, e.g. online hackathon. Plan to discsuss it with Ash, Alexandra, Anais. Maybe bounty program, maybe plus online hackathon.

### Notes
* Helpful reading info: https://github.com/nearprotocol/near-bridge/wiki
* Online hackathons. Unfortunately, online hackathons are not regular. We need to simply google the list. What makes the online hackathon famous is the status of the main company that leads them. Some hackathons last for few weeks, some for few months. We should aim for short hackathons.
* Bounty programs. We should consider gitcoin. The task needs to be very specific and well-defined. The specificity is really important. The motivation for doing bounty is usually about fun and challenge.
* Anton B to think of list of things we would like to fix in third-party dependencies for bounties.

## 06.03.2020

### Agenda

High-level focus with notes:
* ETAs;
* Bridge spec - discuss ETA together with Sherif and Anton B;
* Verifiers QA - March 11-15;
* Near light client - plus 2-3 weeks, need to check the lightclient spec again;
* Token example - requires contracts for both sides and nodejs scripts. March 15-19;
* Cross-contract calls - will rely heavily on incentives, like GSN. First we need to come up with design and someone else can implement it. Near->Eth call can be achieved first because of GSN, design by the end of the March for design+implementation. The opposite Eth->Near call will require implementing GSN, plus one month for design and implementation.


Other notes:
* Anton B. Discussing spec with Sherif. Discussing renaming to disambiguate. Core structure of eth bridge does not cover economic incentives, but we will be able to add this alter on top to allow more use cases;
* Anton B & Evgeny K. Discuss with Sherif the instructions on how to run and test the bridge;
* Anton B also sketched sharded fungible token, following the discussion with Alexey F.

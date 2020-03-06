# Middleware Work Group Agenda and Notes
This thread contains agenda and/or summary of the regular Middleware sync meeting. Please propose agenda items through the PRs and Issues.

## 11.03.2020

### Agenda
???


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

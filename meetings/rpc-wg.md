# RPC Work Group Agenda and Notes
This thread contains agenda and/or summary of the regular RPC sync meeting. Please propose agenda items through the PRs and Issues.

# 06.08.2020

## Current focus
Only Vlad F and Bohdan are working on RPC.
* NEAR Indexer for Wallet. Bohdan is finishing the first iteration of the indexer, the last PR is pending. Trying to setup the indexer for Wallet for Mainnet to check access keys nuances, need help from Bowen W. This would require whitelisting IP addresses. Please don't crash mainnet please :)
* Rosetta -- Vlad F had to drop entirely the idea of using transactions and receipts to derive Rosetta information. Had to throw away the code. Currently is rebuilding it using changes API and derivining balance changing transactions from it. It is not immediately trivial, had to do prefetching of the old state and diff it with new state. Have to simulate subaccounts for Rosetta, e.g. storage balance is a subaccount, and every storage usage becomes a transfer because of that. It takes time to map it out precisely, but there seem to be no unknowns now. Max Z: Might need help from Evgeny K to audit changes. Evgeny K approves. The plan is to finish Rosetta Data API tomorrow, plus or minus. There seems to be already intermediate success because the checker seems to be partially passing. Might still need some time to polish it, several hours;
* JSON RPC -- last week Bowen W reverted PR that was blocking RPC endpoints when node was in syncing. We just need it for when the node just started and very much behind. Bowen W is investigating about 3 issues that cause the node to fall out of sync. These are issues because we are using RPC nodes for bootloading too.

## Notes
* For performance we need to review JSON RPC and ViewClient because we cannot load network through a single JSON RPC;
* We will be using indexer for monitoring the bridge, do deep inspection of the bridge transactions to have alerts and dig into the data. This will be driven by Bohdan, Mario, and Bo. Action Item for Max: setup a channel for this discussion;
* We need to have structured errors not just for the runtime.

# 30.07.2020

## Current focus
* NEAR Indexer. Bohdan K has finished implementation, resolved shortcoming bugs, manually deployed to a GCloud instance, saving all data about access keys (this is new, and was not the case with old explorer. Old explorer works on the level of transactions, which caused issues with multisig on the wallet -- they couldn't observe access keys added to account). Next week Bohdan K will finish README.md and docs, so we will be able to ship indexer for wallet. We will ask Sandi to create deploy strategy and CI;
* Rosetta. Remains proper serialization of data for subaccounts (Rosetta concept). Account balance is liquid balance, and subaccount is locked balance, and staked storage balance is another subaccount. We need to map transfers between these balances as Rosetta operations. Vlad F uses changes API and gave up on following transactions receipts, instead we recreate transactions from changes API;
* JSON RPC. Had some discussion with Bowen W and Max Z about: performance and errors. This whole week our RPC looked unstable to outsiders -- due to abuse of view calls by someone; and Bowen W implemented sanity check that we should serve RPC when node is syncing, but it turned out that nodes are very frequently in the syncing state, e.g. when they are lagging 2 blocks behind (2 secs). Unfortunately, it would take some time to implement to distinguish between very out of sync nodes and slightly out of sync. Bowen W will revert this check first.
  * Medium term goals. First, we will address the errors, make them structured. This is a huge blocker for the Applayer;
  * It seems like we might have a race condition inside RPC-server-ViewClient that results in node reporting "invalid nonce" error. Vlad F: even if we solve it it might be hard to write the test there. near-api-js attempts to resend transaction when RPC timeouts (e.g. due to slow block production).

# 23.07.2020

## Current focus

* NEAR Indexer for Wallet: Bohdan K: the main part syncing access keys is done, but needs some adjustments (README, CI, deployment). There is a struggle with the DB deployed on Render.com (investigating, optimizing it now). Vlad F: had a chat with Vlad G about NEAR Indexer implementation (nearcore compilation time), no action is planned.
* Rosetta: ETA Data API 24.07.2020. Implement fake transactions for events like validation rewards / stake return using chunk hash as Rosetta "transaction hash".
* RPC: a partner had troubles with `validators` RPC call (slowness). Needs to clarify their requirements as it is not clear why they need to hammer it every so often.

## Future work

* NEAR Indexer for Monitoring: Max Z: use NEAR Indexer to collect all possible data into SQL database, so people can use SQL queries to drill down identifying the problems and potential issues proactively (using time series on Spark / Dask)

# 16.07.2020

## Hightlights

* NEAR Indexer Framework is merged! Kudos to Bohdan K

## Current focus

* NEAR Indexer for Wallet: index Access Keys (added and removed), already have a PR from Bohdan K (PoC is ready).
* RosettaRPC: Vlad F works on it. It needs to be clarified things around balances (amounts are expected to be tracked via transaction operations, while we have validators rewards, locked balance). Max Z: We seem to implement an interface of another blockchain on top of NEAR. Max Z: Consider having epoch manager to be a smart contract, we will have transactions/receipts related to the rewards. Evgeny K: We can workaround it using chunk_hash and treat rewards a "block transaction". Max Z: We should still consider the future of having epoch manager as a contract, so we implement rosetta in a future-proof way. Evgeny K: We seem to be able to rely on State Changes exposing all the balance changes. Max Z: Another concern about liquidity of the balance, Rosetta may expect the balance to be fully spendable, which is not the case given the storage rent. Evgeny K: Will write a doc covering all the possible scenarios where the balances can be altered in NEAR.

# 09.07.2020

## Current focus
* Indexer. The work was split on indexer itself (indexer streamer) and explorer indexer. Finished the streamer. Now have receipts in the proper way in the streamer indexer. Haven't merged it to master yet, would need to cherry pick it into betanet and testnet branch. Then the plan to continue working on explorer indexer. Will start preparing indexer readme and specific plugins for indexer for our partners;
* Rosetta. Last week they releazed new version and extended their wallet API (for submitting transactions). Vlad Frolov was finishing it up yesterday and today. No blockers. Vlad Frolov just needs to implement 7 new endpoints. We have more abstractions in NEAR than in Rosetta, so we have to merge some of our abstractions together, e.g. we merged receipts and transactions into one entity.

## Notes
* Max Z: There are some confusions with what is considered to be a successful transaction.

# 26.06.2020

## Current focus
Main focus:
* Rozetta RPC. Vlad Frolov is finishing two missing endpoints -- endpoints that show transactions from mempool to Rozetta. Will be ready early next week;
* Network indexer. Bohdan Kholorets has finished all work for explorer indexer. Need help from Vlad Frolov to run it with real nodes, so that we can populate it with real data and pass it to Yifang Ma so that she can work on the frontend. The timeline will depend on Vlad Frolov's availability, hopefully on Monday;
* JSON RPC. Bowen Wang is helping with timeouts and error messages. Almost done, need to address Vlad Frolov's comments;
* Added executor_id to execution outcome by Bowen Wang. This is protocol breaking, needed for the bridge;

## Notes
* Vlad Frolov: It seems like Rozetta can be used only with local node, since it does not support pagination. Neither for transactions in the block nor as transactions in the mempool. It looks like it is easy to overwhelm Rozetta;
* Vlad Frolov: Now that Bohdan Kholorets and Vlad Frolov know from Bowen Wang what is the difference between signer_id and receiver_id they need to patch some stuff in the indexer.

# 18.06.2020

## Current focus
Mostly focused on two things:
* Rozetta by Vlad F. Defined the models from their spec and defined the endpoints. Working on connecting those to our VievClient. Will be the main focus by Vlad F this and next week. Optimistically should be done mid next week;
* Indexer by Bohdan. Finalizing receipts models for diesel ORM. Almost about to handle them and store in the db. After that will need to handle access keys and accounts and that will be it for the indexer. After that will reach Bo to discuss where and how to deploy it. Optimistically, initial deployment of indexer done next week, which will unblock explorer side of integration for indexer, which will require communication with Yifang. This will include doing migration from SQLite to Postgres. Unfortunately, even though we use ORM wrapper on top of SQLite it does not hide all specifics of the database, like we learnt in the past :(. Overall the migration should be quite straightforward.

## Mid-term focus
* Vlad and Bohdan will address fuzzing findings;
* RPC errors provide more information for the users so that they know when to retry.

## Future focus
* Once we are finished with explorer we plan to provide a dedicated indexer for wallet, to make sure wallet does not depend on the explorer. We want to avoid having a single point of failure. It will also allow to index specific data that wallet neexds. This will also to reduce the dependencies, because currently wallet depends on the internals of explorer in contrast to public API, which is not good. Additionaly, this will allow partners do deep inspection of transactions. This will become very relevant to bridge dashboard.
* We also need to solve the metadata for the contracts. Bring up with contract runtime people. Not super high priority, but a nice feature to have in the future, currently there are not resources on explorer side to integrate it though.

## Notes
* Bowen W: We need to return finality together with execution result from the RPC. Currently the if transaction fails due to old nonce RPC returns timeout.

# 12.06.2020

## Current focus

* Mostly working on JSON RPC, not changing it. Bohdan finished fuzzing, we have a framework in place, merged into master. Identified some issues and inconsistencies. Fuzzing did not reveal any bugs or security issues, just inconsistencies, like returning empty string instead of an error. Bohdan has created all issues on github and we will work on them in order, they are all P2.
* Redesigning AccountId type redesign, previously it was just type alias of String, and it was not self-validating. Fuzzer revealed that we don't have validation in place for many RPC endpoints. Vlad F believes it is possible to craft malicious PRC request and exploit it somehow.
* Vlad F is also working on Rozetta RPC, nothing to merge yet.
* Bohdan is working on the network indexer. Bohdan has implemented storing of the transactions. Had some issues with missing receipts, so decided to use receipts as a central data item. Currently is working on storing receipts in the database, with actions and all accompanying data.

## Notes
* Vlad F, Bohdan and Max Z discussed implementing sequence diagram for receipts;
* Bowen W: we need to double check that access key refund still has sender as "system". Something to sync with Evgeny K;

## Discussing receipts
contract A {
  B.f1().then(C.f2())
}

Alice-->A
A-action->B
A-action->C (but also wait for the data)
B-data->C

# 05.06.2020

## Current focus
* Regarding our JSON RPC we currently only refine it. Bohdan K is working on fuzz testing, it is Phase 1 blocker. Discovered some inconsistencies and mishandling of requests. No crashes or security issues were discovered yet. Overall, we also need infrastructure for such testing; Bohdan K: Finishing fuzzing. Because we are currently getting Ok instead of Errors we are not sure we are not getting crashes behind the scenes. Will finish “invalid methods” test in an hour; Vlad F: Seems like we are finishing the first round of testing, fix them and then we will retry them. Currently we have only found issues that Vlad F has already reported before.
* P1. Rozetta RPC is required for misterwhite. They want us to expose this specific RPC on our node. They have specs and tooling to verify our API. The API is trying to be generic, which might be difficult for blockchains, since there is a lot of specifics on each blockchain. Additionally we have sharding which Rozetta is not designed for. For now the fix is to run Rozetta RPC only on the node that tracks all shards. Vlad F is working on it. The current estimate is to have initial implementation by Wed Jun 10. The hardest part is hiding sharding. Having Rozetta spec really helps;
* Network indexer. Will be design to be an extension of nearcore node, it will be part of the same process and will be enabled with a compiler flag. We design it as sort of a framework. Currently listens to the blocks and dumps data into Postgres; Bohdan K: We have integrated with the client actor to catch the blocks. For the first iteration came up with two creates: streamer -- catches blocks ; indexer -- crate; Currently these two crates are combined. It works by polling the client actor every .5 second and dump the blocks that were produced so far. The framework abstracts out the details and exposes a stream of events that user can save or handle the way they want. The idea behind the framework is to be as least invasive as possible, therefore we use public client interface.

## Notes
* We keep track of all RPC issues in nearcore repo using zenhub. Supposed to be up to date, Vlad F is taking care of it. https://app.zenhub.com/workspaces/chainmiddleware-5cea2bcf78297c385cf0ec81/board?labels=rpc&repos=151331938
* Bohdan K is working on fuzz testing
* https://github.com/nearprotocol/nearcore/pull/2651


# RPC Work Group Agenda and Notes
This thread contains agenda and/or summary of the regular RPC sync meeting. Please propose agenda items through the PRs and Issues.

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


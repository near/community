# Contracts Work Group Agenda and Notes
This thread contains agenda and/or summary of the regular RPC sync meeting. Please propose agenda items through the PRs and Issues.

# 05.06.2020

## Current focus
* Regarding our JSON RPC we currently only refine it. Bohdan K is working on fuzz testing, it is Phase 1 blocker. Discovered some inconsistencies and mishandling of requests. No crashes or security issues were discovered yet. Overall, we also need infrastructure for such testing; Bohdan K: Finishing fuzzing. Because we are currently getting Ok instead of Errors we are not sure we are not getting crashes behind the scenes. Will finish “invalid methods” test in an hour; Vlad F: Seems like we are finishing the first round of testing, fix them and then we will retry them. Currently we have only found issues that Vlad F has already reported before.
* P1. Rozetta RPC is required for misterwhite. They want us to expose this specific RPC on our node. They have specs and tooling to verify our API. The API is trying to be generic, which might be difficult for blockchains, since there is a lot of specifics on each blockchain. Additionally we have sharding which Rozetta is not designed for. For now the fix is to run Rozetta RPC only on the node that tracks all shards. Vlad F is working on it. The current estimate is to have initial implementation by Wed Jun 10. The hardest part is hiding sharding. Having Rozetta spec really helps;
* Network indexer. Will be design to be an extension of nearcore node, it will be part of the same process and will be enabled with a compiler flag. We design it as sort of a framework. Currently listens to the blocks and dumps data into Postgres; Bohdan K: We have integrated with the client actor to catch the blocks. For the first iteration came up with two creates: streamer -- catches blocks ; indexer -- crate; Currently these two crates are combined. It works by polling the client actor every .5 second and dump the blocks that were produced so far. The framework abstracts out the details and exposes a stream of events that user can save or handle the way they want. The idea behind the framework is to be as least invasive as possible, therefore we use public client interface.

## Notes
* We keep track of all RPC issues in nearcore repo using zenhub. Supposed to be up to date, Vlad F is taking care of it. https://app.zenhub.com/workspaces/chainmiddleware-5cea2bcf78297c385cf0ec81/board?labels=rpc&repos=151331938
* Bohdan K is working on fuzz testing
* https://github.com/nearprotocol/nearcore/pull/2651

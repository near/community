# Runtime and Friends Work Group Agenda and Notes
This thread contains agenda and/or summary of the regular Transaction Runtime sync meeting. Please propose agenda items through the PRs and Issues.

The scope of the transaction runtime is the following:
* Routing and storage of the receipts and transactions;
* State;
* Standalone execution;
* Fees, refunds, gas price and conversion of tokens to gas, economics

Applications:
* use-cases from partners
* needs from application developers
* potential improvements for tools
* educational content

Contracts:
* SDK
* JS SDK
* New Contracts
* Standards

## 19.11.2020

- Runtime:
    - A PR to fix param estimator with preparing storage keys before deletion and removing randomness from deployed contracts to be consitent.
    - Events - we can start implementing `logs_base64` on RPC endpoint.
- Applications:
    - Implemented NEP122 for Berryclub. Maybe switch Payload to JSON string from binary Borsh for human-checking
- Contracts:
    - Simulation testing PR is reviewed and looks good.
    - New Promise methods for passing nonces to keys.
    - We'll need to publish a new `near-sdk-rs` version with a few fixes.  
- Standards:
    - NEP122 has to move to vaults and add a Trait for methods. Add payable requirements.


## 12.11.2020 

- Runtime:
    - Fees update still blocked on new params estimation due to some panic during the estimation.
    - Request: implement `DeleteState` action.
    - Discussion about host method to do `trie_iterator` which we commented on to explain the reason why it was removed.
- Applications:
    - Testnet devX/UX is different from Mainnet. Free tokens, lower security for wallets. Needs testnet faucet.
- Contracts: 
    - Staking pools can't be deleted, because they are locked contracts without keys. This is to avoid breaking some guarantees for lockup contracts.
    - Simulation Testing PR is ready for review and blocked on reviews
    - 2FA issues with near-api-js were fixed.
    - 0.25 allowance encourages you to not attach 300Tgas
- Standards:
    - Review of Fungible token standard. Rename safes to vaults (NEP 122).
    - Add view methods to the NEP 122 standard.
    - Add a diagram to NEP 122 to explain transfer flow.
    - Need to explain account registration standard.
    - Discussion/Question: make `transfer` and `transfer_with` payable and require 1 yoctoNEAR for security perspective.

## 05.11.2020

- Fee update PR. Blocked on generating the new config with param estimator.
- Support for Events: https://github.com/nearprotocol/NEPs/issues/130#issuecomment-722516715

## 22.10.2020

- Fee changes -- Eugene Kuzyakov is still working on it; In progress, depends on `nightly_protocol` feature.
- Need to update param estimator to move compilation to exec instead of `send/2 exec/2`
- Re-run estimator with Nikolai changes for on-disk compilation.

## 16.10.2020

- TODO: Final param estimation
    - shift compilation costs into deploy action
    - set runtime compilation cost within function call to 0
    - run it with 10M+ accounts trie with `icount`
    - multiply it by coefficient based on SSD / RAM perf
- TODO: NEP for refactoring Runtime into one block state-based transition
- Running io_costs estimator with 10M accounts.
- Need to adjust pessimistic gas computation by adding a max upper limit of 64.

## 08.10.2020

- PR to refactor `Runtime` class by moving `RuntimeConfig` into `ApplyState`.
- NEP to upgrade fees: https://github.com/nearprotocol/NEPs/issues/120
- RAM-disk vs SSD performance. With 4M accounts in a trie and 400K accounts used with 20 iteration using `time` metric.
- TODO: Final param estimation
    - shift compilation costs into deploy action
    - set runtime compilation cost within function call to 0
    - run it with 10M+ accounts trie with `icount`
    - multiply it by coefficient based on SSD / RAM perf
- TODO: PR for fee upgrade with protocol change
- TODO: NEP for refactoring Runtime into one block state-based transition
- Not happening: Write a NEP for moonglow-like sync transactions within Runtime.

## 01.10.2020

- Fee upgrade PRs are not done this week. Will continue work into the next week.
    - Bo was working on singleton `Cache` for entire node client.
    - TODO (Evgeny): Disk estimation. Compare RAM-disk vs SSD measurements with instructions/wall-time measurements to get the idea about the disk overhead.
    - Bowen: Network and disk from chain is not accounted in the fee estimator.
- Runtime Fee Spec is in progress.
- TODO: Write a NEP for moonglow-like sync transactions within Runtime.
    - Max: Both async and sync calls.
- TODO: Delete Account action change. Next step: write a NEP for this change.

## 24.09.2020

- TODO: Delete Account action change. It currently creates Refund receipt to transfer remaining balance. It will not create an implicit account. Solution is to switch to Transfer action with regular `predecessor_id`.
- Moonglow:
    - Allows to not burn all prepaid gas
    - Don't need to do cross-shard refunds, because it uses the exact amount
    - Q: Do you need to specify gas amount per cross-contract call? If you use global counter per transaction. Your parallel threads can charge this counter post every contract execution. When exceeded, the entire transaction is rolled back and the account is charged for the burned prepaid gas (up to the limit). Allow to dip into negative.
    - We may prioritize gas price priority instead of bunring gas.

## 17.09.2020

- Updated Runtime spec about implicit account creation.
- PR for fee param estimator is merged
- TODO: Add a PR template to nearcore to include extra fields: documentation/spec update, linked issues/neps, devx effects.
- Future work item for upgrading Fees as a protocol change. Move compilation cost to Deploy action.
- Disk compilation cache - mostly done (works as a cache - compiles on call). Need to plug RocksDB and include the current VM-version into the cache-key.


## 10.09.2020

- TODO: Add a PR template to nearcore to include extra fields: documentation/spec update, linked issues/neps, devx effects.
- Published runtime crate `2.2.0`.
- Update to fees param estimator
- TODO: Update Runtime spec about implicit account creation.
- Bowen added more into the docs
- Random seed fix. 
- Discuss upgradability


## 03.09.2020

### Agenda

- Implicit account creation merged with protocol version 35
- TODO: Publish runtime crate `2.2.0`.
- Runtime changes should be backward compatible and hidden behind a protocol version.
- PR for a param estimator fixes.
- Bowen added more info into the TX spec.
- TODO: Add a PR template to nearcore to include extra fields: documentation/spec update, linked issues/neps, devx effects.

## 27.08.2020

### Agenda

- Implicit account creation (PR in progress). https://github.com/nearprotocol/NEPs/pull/71
- Decorators on the fees.
- State changes decorators.
- Bowen is writing a Runtime spec for transactions (for exchanges).

## 20.08.2020

### Agenda
- Burn all prepaid gas or just gas priority. -> still burning the prepaid gas
- DDOS on RPC -> skip
- Fees estimator ->
    - Most actions are very cheap.
    - Non-sir receipts are more expensive. 0.6Tgas
    - Good progress on fees
    - [ ] Do a macro for fees inside the VM code.

### Not discussed (out of time)
- `Comment` action for transfers to exchanges vs standard on `deposit` method `FunctionCall`.
    

## 13.08.2020

- Discussion topic:
    - EVM native support
        - EVM action or native function call
    - Congestion / Gas price auction
    - Transaction nonce to be random instead of integer
    - Large contract deletion (storage cleanup). Pricing - we can take a fee from the storage stake and return less to beneficiary. Execution - might be really slow for large storage.
    - Contract Compilation / Caching

## 06.08.2020

- Invalid state transition due to AVX instruction in Wasmer for floating point operations. Nikolay added a requirement for AVX for Wasmer.
- Discussion on Congestion with delayed receipts. https://github.com/nearprotocol/NEPs/issues/104
- DeFi/EVM - https://gist.github.com/evgenykuzyakov/3dcfb336fadcf242a215f1c16ce82d4e

## 30.07.2020

- Zeropool PR for private TX
- Bowen discovered potential invalid state transitions on different machines
- Discussion on Congestion with delayed receipts.
- 

## 23.07.2020

* Zeropool PR for private TXs.
  * Use `borsh` within Runtime to parse the input within a host function.
  * To remove all `f64` usage and replace with int math.
  * Minimize crate changes from the audited version.
* Having external/host methods as Wasm pre-compiled modules. Other option is to load another module as a linked wasm module to save size.
* Check out proposal for requirements/guidelines for a new protocol change features: https://github.com/nearprotocol/NEPs/issues/99 

## 16.07.2020

### Notes
* Published `1.0.0` for `near-vm-logic` and other runtime crates. Included contract compilation fees and borsh `0.7.0`.
* We need to decrease fees `validator_stake_base` and `validator_total_stake_base`, because every call is about `10 ** 12` Gas. So for 100 validators it's almost `100` TGas.
* Upgradability discussions

## 09.07.2020

### Notes
* Contract compilation fees are in. Willem Wyndham said that fee computation should not be affected by `no_cache` issue;
* Alexey Frolov proposed: https://github.com/nearprotocol/NEPs/issues/90 ;
* Are we getting rid of UTF8 logs? Evgeny Kuzyakov -- we can do loss conversion to UTF8. Should we expose binary log in addition to string log. Evgeny is not sure we should mix it. Ideally we should not merkelize logs and should merkelize binary events only. The least breaking solution is to expose binary event in addition to logs. If we remove merkelization of logs then we need to recalculate the pricing of the fees. Max Z: This change is a good candidate to test upgradability. Alexey Frolov suggests to consider the bloom filter to simplify subscriptions. Max Z: Another good candidate to test ugpradability.

## 26.06.2020

### Status report
* Evgeny Kuzyakov exposed total token burn that was taken from the account for the given execution -- we needed it for the reporting;
* Bowen Wang added executor_id to execution outcome. It is needed for the bridge, but also it now allows to query receipt id from the shard since executor_id exposes the shard where the receipt/transaction was executed. Evgeny Kuzyakov: we would need to expose the block height. Bowen Wang: We don't need it since, we already record block hash;
* Nikolay Igotti -- Willem Wyndham is working on adding contract size fee. Nikolay Igotti adding the fee is not actually the problem but it broke our cost estimator, there is a very dangerous error message "trie node is missing". Bowen Wang: this seems like a very dangerous error message, good thing it is reproducible, we need to investigate it. Sounds like P0.

### Action item
* Maksym Zavershynskyi We need to escalate the exposed P0 issue. Bowen Wang kindly volunteered to take a look;
* Add param estimator to the CI. Nikolay Igotti needs to discuss with Bo Yao how to enable it on CI.

### Notes
* Bowen Wang since we currently enforce having delete account as the last action, we don't change the actor_id during receipt execution, so some of our code right now is redundant. Vlad Frolov and Evgeny Kuzyakov think that we should keep this code around.

## 19.06.2020

### Status report
* Evgeny K: We merged the dynamic gas PR, it first seemed like we broke nightly tests, but they might have been broken already, so we need to investigate it more.
* Evgeny K: It seems like we are mostly done for Phase 1 overall. Bowen W has added overflow check last week;
* Bowen W: The million proposal attack fix is pushed already.
* Evgeny K: Pushed new Borsh version this week, so we need to update nearcore and near-sdk-rs with new version. Vlad F has added Cow support for accounts, so we might want to wait for it.

### Notes
* Unfortunate side effect for the dependabot, we need to push borsh and near-sdk-rs simultaneously. Vlad F will help with re-exporting borsh in near-sdk-rs, see: https://github.com/near/near-sdk-rs/issues/142#issuecomment-633754970
* Bowen W: we need to introduce extra field in the `ExecutionOutcome`, it is protocol breaking change. This changes bridge too, we would have adapt the deserialization in Solidity;
* Evgeny K: Since we have multiple independent change logs in nearcore we need to figure out how to correctly connect it with dependabot;
* Bowen W: we still hasn't resolved the issue of having lots of proposals in block header. Staking pools will be restaking themselves because of the people delegating, which might create significant issues.
* Evgeny K: Let's discuss the issue of headers and proposal during the research meeting.

## 12.06.2020

### Status report
* Evgeny K: Created a dynamic gas PR. The idea is that we estimate the max number of blocks the transaction can last and try to limit this number to 64 blocks. Then we have an inflation coefficient slightly higher than the actual inflation gas price coefficient and we strictly use the price of the final block (instead of integrating an exponent). This will break some stuff, e.g. ExecutionOutcome. ExecutionOutcome returns that amount of gas that was burnt, but does not report the price at which the gas was burnt. Stuff gets complex when actual gas price on the block gets higher than pessimistic gas price, Evgeny K will update the spec;
  * Interesting side effect of the pessimistic gas pricing -- even simple transfers will generate gas refund. We might need to recompute the fees with the new logic -- need to sync with Nikolay I.
* Bowen W: Fixed gas price possible overflow by adding a limit;
* Alexey F: Added proposal to not validate actions before they get into the chunk, see: https://github.com/nearprotocol/NEPs/issues/84

## 05.06.2020

### High-level focus
* Evgeny K: Fixed allowance for the access keys, now they are refunded the same way we refund the account;
* Evgeny K: Started working on dynamic gas. Started adding a function that computes pessimistic gas price, unfortunately it involves rational numbers that need to be taken to the power, decided to try "big rational" library. Lots of checked arithmetics. Limiting the depth of execution to 32. This needs to be communicated to DevX team. We should make sure the rounding is easily explainable in the spec and reproducible in other languages;
* Security vulnerability about multiple account deletions in one batch action needs to be fixed in Phase 1;
* Million proposal attack needs to be handled partially in the transaction runtime -- Bowen W (Avoiding copying proposal in epoch manager needs to be done. Threshold for minimal stake, deduplicating proposals in the runtime is already done);
* Bowen W asked Mikhail to write spec for the state.

### Notes:
* Bowen W: At the beginning of the epoch all validators have their accounts updated, this seems to be slow;
* Max Z: Gas price overflow. Is it a problem? Evgeny K: We can have max gas price above which we do not inflate.

### Action items
* Communicate the prepaid gas changes to the DevX team;
* Introduce max gas price;

## 29.05.2020

### High-level focus

* Congestion issue. We have a solution in NEP 67, see https://github.com/nearprotocol/NEPs/issues/67#issuecomment-634853177 the high-level idea is to upcharge the gas at the beginning. It takes the inflation of the gas cost (currently 1% per block if blocks are more than half full). We calculate the worst-case scenario of what is the maximum number of cross contract hops the transaction can do and we assume the highest inflation. We "buy" gas using pessimictic estimation, i.e. stake gas, and then we try implementing refund logic such that the actual incurred cost, after refund, is the actual computation cost. This requires several changes.
  * First we need to fix the below bug;
  * Then we need to implement the actual new design.
The benefits of the proposed design is that it does not change the implementation and protocol much.
We would also need to increase the base fee of sending the contract call so that the deepest the cross-contract recursion can go is ~32 hops.
* We need to fix a bug where refunds do not refill access key allowance. Evgeny K has implemented a PR that needs a review.
* The new upgradability design does not actually change the staking action. Instead the voting for the new version happens by chunk producers.


### Notes
* Alexey F: What happens if the future gas price is actually higher than the worst case price? Evgeny K: Currently we will just not issue a refund in this case. It kinda creates an opportunity for an attack but it will be very costly.
* Max Z: Is our inflation rate too high? Evgeny K: It is not too high, we can assume even more pessimistic scenario. Bowen W: Currently it can actually overflow and we need to fix it.
* Max Z: How did we pick params for the gas inflation? 1%, 50% fullness, exponential growth. Why not 0.1%, 0.01%? Alexey F: can gas cost inflation depend on the size of the queue? Evgeny K: We cannot actually bump the gas cost too sharply because it will cause too high slipage.
* Bowen W: We need to have a temporary solution on how many proposal we can generate per block header. Header size is 17KiB without proposals.

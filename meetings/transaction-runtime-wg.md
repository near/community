# Transaction Runtime Work Group Agenda and Notes
This thread contains agenda and/or summary of the regular Transaction Runtime sync meeting. Please propose agenda items through the PRs and Issues.

The scope of the transaction runtime is the following:
* Routing and storage of the receipts and transactions;
* State;
* Standalone execution;
* Fees, refunds, gas price and conversion of tokens to gas, economics

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

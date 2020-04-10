# Ethereum-Near Bridge
This thread contains agenda and/or summary of the regular Bridge sync meeting. Please propose agenda items through the PRs and Issues.

Led and executed by Anton B.

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

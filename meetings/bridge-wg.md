# Ethereum-Near Bridge
This thread contains agenda and/or summary of the regular Bridge sync meeting. Please propose agenda items through the PRs and Issues.

## 12.03.2020

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

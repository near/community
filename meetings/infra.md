# Near Infra

This thread contains agenda and/or summary of the regular Infra sync meeting. Please propose agenda items through the PRs and Issues.

The meetings are scheduled for Thursday at 8PM CET and you can find the event invite on the NEAR Community calendar.

The scope of the infra team is the following:
 - Releases and Testing 
    - Managing releases for NEAR products
    - Communicating with the community about changes in the protocol (hard-forks and similiar)
    - Building and maintaining the testing infrastructure
 - Infrastructure as code for NEAR products
    - Providing IaC configurations and tooling (monitoring, deploying, reliability, etc.) for the community to easily spawn and manage NEAR products
 - Site Reliability and Operations of the networks
    - Oncall, troubleshooting and emergency response for Betanet, Testnet, Mainnet and other NEAR products
    - Alerting and monitoring
    - Defining SLOs and risk management
    - Building tooling for profiling, debugging, logging and regression testing
 - Nearup
    - Development and maintainance of nearup


Short and medium term goals:
- Manage scheduled releases and CI pipelines
- Working with the community to build automation tooling
- Building a stable version of nearup with systemd support
- Provide open-source IaC for the community to use including Terraform configurations, Ansible Playbooks, CI/CD configurations, Kubernetes deployments and similiar
- Immidiate incident response 24/7

## October 8th 2020
### Weekly team updates:
- Nick has been mostly focused on the Terraform configuration for deploying RPC nodes, he created a design doc on how the RPC nodes should be deployed. We want to support any cloud providers with deployments per availability zones to ensure downtime and overload of RPC nodes as seen in past. The plan is to open-source this and provide Terraform configurations which everybody can use.
- Autoscalling is a bit of a problem, as the RPC nodes are not stateless and we need to find a way to collect the state before starting the autoscalled RPC nodes.
- We plan to test this out on crashnet first and than move to other networks.

- Mario has finished the hard-fork automation and worked on getting the hard-fork on testnet done. Working on nearup support for rainbow bridge, as it still is pinned on the v1 on master. He has issues with a test he is trying to debug. Archival nodes have backups now on testnet

- Sandi has worked on weekly release and the hardfork, spent bucnh of time on the testnet down SEV and mainnet RPC overload SEV.I was writing postmortems for all of them and incident review meeting. Canary were broken due to hardfork, fixing logs issue on the canaries, debugging network indexer after hard-fork, rainbow bridge nearup pinning added, added crashnet support to nearup, added archival on testnet, upgraded bulldozer which was broken due to a github API change, oncall.

### Priorities for the next week:
- Nick will continue working on this to get the RPC nodes done first until next week.
- Mario has to figure out what is broken with the test and add the support for nearup v1, he is rewriting the test, as it is hard to debug it right now, once this is resolved he plans to setup automation for rainbow bridge deployments and start looking into setting up monitoring for bridge. Also add mainnet archival nodes backup.
- Sandi will be cleaning up nearup issues filed form community (binary is being downloaded every time, still looking into the logging for neard with logrotate, get testing up to 80%)

### Discussion notes:
Nearup questions:
1. nearup on mainnet, thats going to happen, but only for RPC and boot nodes
2. nearup on mac, thats a question we have to figure out, is it worth doing? cross compilation from linux on mac is causing issues. follow up with illia on that
3. discuss windows support which is unlikely, but we can check and discuss this, wasmer doesn't compile to windows :)
4. nearup with systemd support and logrotate as an option, but it will need sudo access on initialization, we need to check if that still fine

Open source:
First start with open sourcing the terraform, open source collection of grafana dashboards.
We need better communication with validators on what do they need or what is missing. 
Creating more posts and blogs about how to setup infrastructure in general, like DOcean does.
Make backups for data, more accessible to the community, publicly hosted.

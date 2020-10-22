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

- Sandi has worked on weekly release and the hardfork, spent bunch of time on the testnet down SEV and mainnet RPC overload SEV.He was writing postmortems for all of them and incident review meeting. Canary were broken due to hardfork, fixing logs issue on the canaries, debugging network indexer after hard-fork, rainbow bridge nearup pinning added, added crashnet support to nearup, added archival on testnet, upgraded bulldozer which was broken due to a github API change, oncall.

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

## October 15th 2020
### Weekly team updates:
Nick has a first version of the Terraform configuration for RPC, figuring auto-scaling is non-trivial and we are having discussions how to replicate the data while spawning new RPC nodes with nodes being distributed across zones. Archival node not attached to the GLB which will be snapshotting the data and the new nodes will just sync from that snapshot. 
Mario added and fixed monitoring and graylog to mainnet, minor oncall issues, added latency graphs for RPC.
Sandi weekly release, added mainnet support to deploy scripts, working on a PR to add mainnet support for nearup, added logrotate for nearup, Setup discourse. Rest of the time off. 

### Priorities for the next week:
Nick will land the first version and test if it out works on crashnet first. Test scenenarios of adding nodes, removing nodes, etc. 
Mario more logging and better monitoring for detecting what is causing RPC to fail. Monitoring for bridge.
Sandi: finalize mainnet support, users created issues on nearup clean up. 


### Discussion notes:
What is the actual bottleneck on the RPC is nodes is unknown. We currently have the RPS per endpoint, but don't have latency per endpoint. Look into adding this to grafana? Label endpoint in the prometheus call with endpoint.

We need to change the GLB /status to use /healthcheck instead. Also we have to implement /healthcheck to fail on node syncing.

Betanet with become a bleeding edge network and centralized. Bleeding edge also means with all the feature flags being enabled.

Biweekly release sync has been cancelled and we should use this meeting for coordinating releases. Incident reviews are moved to Monday.

## October 22th 2020

### Weekly team updates:
Sandi:
- oncall setup
- mainnet support for nearup
- fixing an issue with bulldozer after the repo migration
- looked into gperf setup on one machine
- talks with devx about rpc infra
- pagerduty slack integration

Mario:
- mainnet updates are currently manual, so that is taking additional time -> should change with terraform migration
- bug in archival nodes in mainnet release (issue discussion)

Nick:
- helping Sandi to figure out the oncall setup
- first version of terraform has landed, demo of the prototype and discussions about snapshotting. We need to stop neard (issue discussion). Figure out the load balancing properly, as the current configuration only allows to point a load balancer to only one region which is suboptimal as we want a global load balancer.



### Priorities for the next week:

Mario:
- RPC observability: RPC latency per route and other metrics
- investigate the bug: neard terminates the RPC connection causing the healthchecks to fail? make an issue writeup: link the logs.
- migration for nearup to mainnet (figure out phased rollouts)

Nick:
- Rollout of the RPC Terraform to crashnet/betanet

Sandi:
- finalize the systemd and configuration support for nearup
- finalize the oncall schedule
- migration for nearup to mainnet (figure out phased rollouts)
- most likely help debug current RPC issues: backup issue, connection termination issue, migration issue, ...
- figure out if the migrating commit path is executed.

### Discussion notes:
Terraform migration for betanet. Just make sure to have a seperate GLB and rediredirect the DNS, figure out if we can reuse the certifactes (99percent it does).


Any RPC related questions we haven't covered before tomorrows RPC meeting?
- Archival nodes issue while migrating. Figure out if the code path after the commit gets executed.
- Backup is not really a backup issue. :)
- Connection termination issue on RPC nodes.
- Discuss archival vs non-archival nodes discussion for tomorrow. 
- Archival nodes are running a hotfix and we have to figure out a way to upgrade that. The new release should fix this across the network.


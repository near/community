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


### May 14th 2021
### Weekly team updates:
- Started rollout of wasmer 1.0 to testnet (ETA 2 weeks for full rollout); @chefsale @mhalambek @matklad
- Synced with aurora team on infrastructure related questions; @chefsale @mhalambek @artob
- Created 20 debugs nodes on testnet for chain team; @mhalambek @bowenwang
- Indexer infrastructure fully automated on both testnet and mainnet; @chefsale @frol
- Small bugfixes across the infra; @mhalambek @chefsale


### April 1st 2021:
### Weekly team updates:

Sandi & Mario:
- Aurora Testnet hard-fork preparations
- Infra OKRs
- Team SLOs in progress

### Priorities for the next week:
- Milestone planning to figure out what is next after milestones are finalized on Tuesday

### March 18th 2021:

### Weekly team updates:
Sandi: 
- migrated all the CI scripts from nearcore to near-ops with some fixes and improvements
- incident on betanet due to account v1 change (hardfork and mitigation).
- fixed deploy-ci-betanet 
- created a terraform configuration for wasmer1 canary nodes on testnet and mainnet.
- started rewriting the alerts
- implemented a smarter artifacts checks for the betanet deploy script
- created a list of OKRs for Q2

 Mario:
- fix public backup due to a incosistent terraform state (disk cleanup issue)
- mainnet and testnet canary with terraform and fresh data with a binary URL, takes less than 30 minutes to spin up new machines with custom binary
- created a new repo near/local for local setup via docker compose, wrapping up local docker setup, still some polish missing around wallet and bridge frontend
- bridge DNS setup
- geth minor flags and enable miner for ropsten

### Priorities for the next week:
Sandi:
- Rewrite of the health/status endpoint
- Finish rewriting the alerts
- Refactor of the CI deploy scripts
- OKR planning

Mario:
- finalize bridge e2e tests
- Q2 planning
- Hunting down assistance for bridge wallet debugging, sending funds doesn't work locally


### February 26th 2021:

### Weekly team updates:
Sandi:
- fossa integration ~10 repos left, biggest issue still nearcore and transitive dependencies on nearcore 
- improved CI for indexers (linters, testing, style, fossa) and nearcore (debug symbols on betanet and testnet enabled, disabled landing with warnings, fixed broken mac osx and refactor)

Mario:
- Increase log storage on machine.
- Adjust loadbalancer configuration new version (switched to RPS based balacing instead of CPU, data looks good so far, still need more signal)
- Down-scalled over-provisioned regions.
- Running bridge-frontend locally for testing.

### Priorities for the next week:
Sandi:
- still finalizing fossa integration
- milestone planning and cost reduction 

Mario:
- Bridge
- Milestone planning

### Notes
- backups nodes snapshots are used on RPC infra, so the RPC disks have to be `>= than the backup disks` to rescaled the RPCs.

### February 19th 2021:

### Weekly team updates:
Mario:
- Graylog debugging new setup issues, loadbalancer send all logs to single instance.
- Nginx collect all requests and repsonses for timeout investigation.
- Modify loadbalancer to reduce timeouts, reduced the utilization target from 80% to 70%.
- Sentry bridge error monitoring frontend.
- Repair auto restart for testnet and mainnet.
- oncall: improving the causes of alerts, 50% reduction. CPU alerts for archival nodes reach high utilization without traffic.

Sandi:
- added support for the perf release pipeline running on betanet and testnet
- canary and betanet broken due to https://github.com/near/nearcore/pull/3964
- mainnet nodes timeouts due to `broadcast_tx_commit` timeouts
- betanet broken due to a bug in the new perf release pipeline
- integrated several more repos for EVM team, synced with other teams which are working on this.
- mainnet high CPU usage profiler debug with Frol
- added non-buffered systemd log striping

### Priorities for the next week:
Mario:
- Sentry bridge monitoring latency.
- Graylog repairs/debugging.

Sandi:
- finalize fossa intergrations
- nearcore build improvements: https://github.com/near/near-ops/issues/400, https://github.com/near/near-ops/issues/401
- start working on cost optimisation in March
- send-out incident reviews

Notes:
- Q2 project seperation for different projects.
- check if the policies are correct, split between Apache and GPL.

### February 11th 2021:

### Weekly team updates:

Mario: 
- Fully setup graylog and elastic search operational now with all the DEBUG logs enabled on all the networks (with SSL)
- Solved the coloring issue for logging on the machines

Sandi:
- Fossa integration all repos investigated and issues sent out, fossa integration hopefully by end of February.
- SLOs planning with team leaders
- Oncall and incident review
- Cost Optimization review

### Priorities for the next week:

Mario: 
- Rainbow Bridge frontend monitoringsynced with Vlad Grichina to setup sentry for the frontend
- Rainbow Bridge E2E tests

Sandi:
- Send out PRs for reamaining fossa CI integrations
- Sync with Peter and Ash on the SLOs review, maybe write a forum update on SLOs
- Figure out a plan to reduce costs on GCP
- Improve nearcore CI based on matklads proposals


### February 5th 2021:

### Weekly team updates:
Mario: 
- Graylog with sharded ElasticSearch cluster PoC AWS. 
- Sorting out how to monitor bridge frontend, created a post on the gov.near.org forum
- Archival nodes for The Graph integration. 
- Archival nodes for testnet indexer refactor. 

Sandi:
- several SEVs happening on testnet where block production either goes to zero or CPU load is high, but not due to RPC (frol verified)
- fixed CORS SEV with Mario
- covered 42 fossa repos, 9 integrated with fossa, sentout 18 issues to owners to fix their repositories
- migrated nearcore build, test, release and canary scripts from buildkite to the repos
- fixing the canary nodes going down
- help Dani setup grants DNS settings
- oncall and incident rewamp 


### Priorities for the next week:

Mario: 
- Finalize frontend monitoring after gathering inputs. 
- Graylog adding loadbalancer for higher throughput 
and polishing the whole setup.

Sandi:
- talk to every service owner and make sure the compliance issues get resolved
- discuss oncall, incident reviews and ownership and create a proposal doc
- continue with the fossa integrations and buildkite migrations
- next week incident review


### January 28st 2021:

### Weekly team updates:
Sandi:
- created a organizational spreadsheet for tracking the fossa integration and working on collecting all the owners and data
- generated reports for 80% of the repositories, some of the didn't work, after looking seems like all the python ones and some rust failed, but will investigate more
- started sending first issues on github for resolving the repositories with concerns on Fossa
- fully integrated several repos with fossa including: near/borsh-js, near/near-ledger-js, near/near-api-js, near/docs.
- gave a presentation about NEAR for Croatian Blockchain Association

Mario:
- Mainnet release (rpc and archive nodes)
- Fixed running terraform on AWX
- Public backups are running again for testnet/mainnet rpc and archive
- Update all alerts to be in sync with new infrastructure
- Auto restart neard node via systemd on testnet and mainnet
- New alerts for GCP loadbalancer latency


### Priorities for the next week:
Sandi:
- continue with the fossa integration
- milestone planning
- oncall activities

Mario:
- New sharded/cluster graylog centralized logging setup
- Bridge if it comes up, bridge itself is still being stabilized
- Milestone planning...


### Discussion
- monitoring for wallet and web services in general (privacy in mind, maybe e2e testing) raise this issue in gov.near.org

### January 21st 2021:

### Weekly team updates:

Sandi:
- New release of nearup 0.8.1 fixing several issues:
  - added automatic restarts on genesis change
  - fixed the issue where the config.json is not updated on restart
  - enabled verbose logging for betanet validators by default for better debugging
  - added native mac support which was deleted due mac support release issues
  - fixed test issues and updated the documentation
  - working on disabling coloring for nearup by default

- Added coredumps to the packer images for RPC
- Added nightly macosx release pipeline to the Nearcore CI

Mario:
- Migrate mainnet rpc to new node setup managed fully by terraform
- Implemented faster backups, enabled the also for rpc backups
- Added tags for running AWX on grafana
- Resized AWX with bigger disk and more cpu
- Resized indexer instance 4 vCPU => 8 vCPU
- Added promethus configuration for bridge instances

### Priorities for the next week:

Sandi:
- Write an automation script for initializing all the repos with the fossa initialization config and start landing PRs for all the repos
- Setup a coordination spreadsheet for all the teams involved in the fossa integration

Mario:
- Debug running terraform on AWX
- Bridge dashboards and alerts, help out marcelo if needed
- Revision of alerts for mainnet and testnet to sync/verify new setup

### Discussion
- alerting for wallet and explorer, explorer currently uses statuspal which doesn't support pagerduty integration (https://github.com/near/near-ops/issues/352, https://github.com/near/near-ops/issues/351) cc: @stefano about the walllet.


## January 14th 2021
### Weekly team updates:
Sandi:
- fixing the CI failure caused by file descriptor limit on buildkite
- fixed the Ethereum chain id issue which was not supported for betanet
- added support for coredumps and added ansible automation, currently only rolled out on betanet. coredumps can be found in /var/crash.
- fixed a small bug in the hardfork betanet script

Mario:
- landing new RPC infra managed by terraform only for public rpc endpoint, still has to migrate the internal
- adjusted the prometheus config to fix service discovery to pick up missing nodes
- mainnet is now migrated to systemd

### Priorities for the next week:
Sandi:
- talk to Max about compliance (licencing) across all the NEAR repos
- automated uploads and cleanups of the coredumps on the machines becuase they are quite big.
- oncall

Mario:
- increase the cadence of the snapshots
- rainbow bridge: sync up with the bridge team for end2end testing and frontend monitoring (Alex S.)
- look into possible backup node issues on mainnet only

## Jan 7th, 2021

### Weekly update

Sandi:
- add documentation on how to run archival nodes
- backups
- investigate why nearcore CI fails with too many files opened

TODO: keep investigating the CI failure; investigate neard version and tag mismatch; license compliance work; looking into adding coredumps

Mario:
- migrate testnet nodes to use systemd from nearup. New labels for alerting (differentiate validator nodes from rpc nodes)
- change playbooks for backups w/ systemd change
- Explorer indexer monitoring

TODO: deploy new testnet rpc nodes and reroute traffic to them

## Dec 17th 2020

### Weekly update

Sandi:
- oncall work; improve oncall handbook
- figure out issues with profiler automation
- set up rpc node for investigating rpc timeout

TODO: further improvements on oncall handbook; rpc timeout investigation

Mario:
- finish setting up ethereum node (ropsten and rinkeby) for bridge
- PR to add basic monitoring for the relayers; bridge team will add more metrics
- help Sandi with profiler setup
- fix issue with monitoring and graylog setup on mainnet
- oncall handbooks for backups

### Discussion

There is some issue on testnet with rpc. If the requests are submitted to a separate node not under load balancer, it works. However, it doesn't work if the requests are submitted to the loadbalancer. Frol tried to submit requests to the nodes behind load balancer directly but still experiences "Transaction Expired" errors. Some stats on the timeouts per method could be helpful


## Dec 10th 2020

### Weekly update

Sandi:
- Finalize the profiler setup. Generate both call graphs and flamegraphs and upload them to gcloud storage. Will create a pipeline that does this every 4 hours.
- Start writing oncall handbooks. Added some instructions for betanet, testnet & mainnet

TODO: Setup some nodes to direct some traffic to test a modified version of neard on the issue of rpc timeout

Mario:
- Started 10 backups on testnet. Observed timeout for rpc nodes. Timeouts disappear when the backup nodes finished syncing
- Templated dashboard for grafana. Important for alerts. Graph must have a 1:1 correspondence to alerts
- Started an ethereum node. Done through terraform and packer. Working on monitoring of the node. Figured out types of eth nodes
- terraform for rpc nodes. Testnet doesn't have the same neard as mainnet. Switch from using names to labels

TODO: dashboard for ethereum nodes. Monitoring and alerts for bridge relayers

## Dec 3rd 2020

### Weekly update

Sandi:
- Finish betanet pipeline hardfork pipeline. Need to be able to trigger awx from buildkite for the hard fork.
- Fixed betanet ci pipeline for applayer: it didn't use nightly before & some bug in docker image. Need to deprecate docker and refactor the pipeline
- Fix mac builds for nearcore CI. The github action failed because it wasn't maintained anymore. Found some other action and used it instead.
- Work on oncall handbook

TODO: profiler automation. Need to figure out why some symbols were unknown. Rpc timeout investigation

Mario:
- oncall duties
- Some rpc node gets stuck (not getting new blocks) on mainnet
- Managed to make public backups.
- Improve systemd to restart nodes, memory & cpu utilization control. Adding coredumps. We can make packer json file public.

TODO: Ethereum full node & bridge monitoring

### Discussion

Bowen discussed with frol on the rpc timeout issue. Frol encountered the timeout while he is working on RosettaRPC. Have some hypothesis but need to verify it. Need to follow up on the exact strategy on how to test it.



## Nov 19th 2020

### Weekly update
Sandi:
- oncall
- disable color in logs
- set up profiler machines on testnet and mainnet. Setting up automation to collect flamegraph and call graph daily
- cron job for betanet nightly release
- pipeline for mainnet binary and metadata so that we nearup can work well on mainnet

Mario:
- Mainnet updates. Update archival nodes
- Terraform for rpc. Problem with fast scaling
- Bridge monitoring
- debugging public backups

### Discussion

hardfork pipeline for betanet when there is a protocol change.
Bridge monitoring: prometheus poc


## Nov 12th 2020
### Weekly team updates:

Sandi:
- Betanet hardfork. Created a separate network with four validators and change the deployment pipeline
- no color log flag for neard
- debugging CI issue: some tests pass on CI but fail locally. `--no-cache` if turned on, then the test passes.

Mario:
- public backups for both testnet and mainnet
- bridge tests & linter
- testnet rpc issues: sometimes it gets timeout
- coredumps

### Discussion
Want to do a hard fork on betanet whenever `PROTOCOL_VERSION` behind the nightly protocol feature flag is increased. Dump state and restart the network. Check if the variable has changed in the new commit.

Rpc health check:
1) switch load balancer health check to use /health endpoint
2) release the PR that would mark nodes that are syncing to be not healthy

Cherry-pick commits into the next release:
1) no-color-log flag
2) improved healthe check
3) monitoring improvements

One validator brought up an idea w.r.t centralized log collection service. We can use graylog for this.



## Nov 5th 2020
### Weekly team updates:
Nick:
- Terraform setup & test on crashnet. Initialize new nodes that are spawned. Use packer image to start the nodes. Created an epic for the work on rpc nodes infra

Sandi:
- Split nightly and stable releases. Nightly takes master and builds with nightly features enabled. CI for both with nightly features and without feature flags. Fix certificate on nearprotocol.com.
- Testnet release
- change to deploy scripts. Change from pulling from release page to pulling from master
- CI pipeline for nearup for mainnet: update S3 files
- betanet hardfork: 1) prepare the announcement 2) generate new genesis 3) shutdown current nodes 4) Distribute tokens and let new validators join

Mario:
- Publicly available archival node backups
- coredumps
- rpc benchmark investigation. Used gas_price for benchmark and rpc nodes work fine up to tens of thousands of rps.

### Discussion

We hardcode bootnodes into config.json. We don't know ip addresses of nodes before they are provisioned --> addressed by using static ip addresses



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


# Near Infra

This thread contains agenda and/or summary of the regular Infra sync meeting. Please propose agenda items through the PRs and Issues.

The meetings are schedule for Thursday at 7PM CET and you can find the event invite on the NEAR Community calendar.

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

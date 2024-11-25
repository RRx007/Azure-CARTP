- Azure's automation service allows to automate tasks for Azure resources, on-prem infra and other cloud providers.
- supports Process Automation using Runbooks, Configuration Management(supports DSC), update management and shared resources(credentials, certificates, connections etc) for both windows and linux resources hosted in Azure and on-prem

Some common scenarios for automation as per MS:
- Deploy VMs across a hybrid environment using run books.
- Identify configuration changes 
- Configure VMs
- Retrieve Inventory

## Managed Identity 
- managed identity is used to provide authentication for managing azure resources
- Now retired, Run As Account used to have the contributor role in the current subscription
- Many organizations simply use the same permissions for managed identity now 
- the managed identity can only be used from inside a Runbook os contributer on a runbook == profit!!

## Runbook
- contains the automation logic and code that you want to execute 
- Azure provides both Graphical and Textual Runbooks (Powershell, powershell workflow and python)
- you can use shared resources (credentials, certificates, connections) an the privilges of the Run As account from a runbook
- Always Checkout runbooks! They often have credentials that are not stored in the shared resources.
- by default only signed scripts can be on a VM
- Runbooks can run in Azure Sandbox or a Hybrid Runbook Worker.

## Hybrid Worker
- this is used when a Runbook is to be ran in non-azure machine. 
- a user -defined hybrid runbook is a member of a hybrid worker group
- Log Analytics Agent is deployed on the VM to register it as a Hybrid Worker 
- the hybrid worker jobs run as SYSTEM on Windows automation account. 

`https://medium.com/cepheisecurity/abusing-azure-dsc-remote-code-execution-and-privilege-escalation-ab8c35dd04fe`
- Azure Cli is a set of commands used to create and manage Azure resources 
- can be installed on multiple platforms and can be used with multiple clouds
- Available in Cloud Shell too
- Install using MSI: ` https://learn.microsoft.com/en-us/cli/azure/install-azure-cli `

To use `az cli` we must connect to Entra Id
- ` az login `

Using creds from command line (Service Principal and managed identity for vms is also supported)
- ` az login -u test@defcophq.onmicrosoft.com -p <pass> `

If the user has no permissions on the subscription:
- ` az login -u test@defcophq.onmicrosoft.com -p <pass> --allow-no-subscriptions `

You can configure az cli to set some default behaviour:
- ` az configure `

We can search for popular commands (based on user telemetry)
- to find popular commands for VMs: `az find "vm"`
- to find popular command within `az vm`: ` az find "az vm list"`

We can format output using the ` --output` parameter. The default format is JSON.

List all users in Entra Id and format output in tabl: 
- ` az ad user list --output table `

List only the userPrincipalName and givenName(case sensative) for all users in Entra Id and format output in table. Az Cli uses JMESPath query
- ` az ad user list --query "[].[userPrincipalName,displayName]" --output table `

List only the userPrincipalName and givenName(case sensative) for all users in Entra Id, rename the properties and format output in table
- ` az ad user list --query "[].[UPN:userPrincipalName,Name:displayName]" --output table ` 

We can use JMESPath query on the results of JSON output. Add --query-examples at the end of any command to see examples:
- ` az ad user show list --query-examples `

Get details of the current tenant (uses the account extension)
- ` az account tenat list `

Get details of current subscription
- ` az account subscription list `

List the current signed-in user 
- ` az ad signed-in-user show `


## AAD Users
Enumerate all users
- ` az ad user list `
- ` az ad user list --query "[].[displayName]" -o table `

Enumerate a specific user (lists all attributes)
- ` az ad user show --id test@defcophq.onmicrosoft.com `

Search for users who contain the word `admin` in their Display name:
- ` az ad user list --query "[?contains(displayName,admin)].displayName" `

When using powershell search for user that contain `admin` in their display name
` az ad user list | ConvertFrom-Json | %{$_.displayName -match "admin"} `

All Users who are synced from on-premise
- ` az ad user list --query "[?onPremisesSecurityIdentifier!=null].displayName" `

All Users who are from Entra Id:
- ` az ad user list --query "[?onPremisesSecurityIdentifier==null].displayName" `

## AAD Groups
List all Groups
- ` az ad group list  `
- ` az ad group list --query "[].[displayName]" -o table `

Enumerate a specific group using display name or object id:
- ` az ad group show -g "VM Admins" `
-  ` az ad group show -g <objectid> `

Search from groups that contain the word ` admin` in their display name -run from cmd
- ` az ad group list --query "[?contains(displayName,'admin')].displayName" `

When using PS search from groups that contain the word `admin` in their display name.
- ` az ad group list | Convert-Json | %{$_.displayName -match "admin"} `

All groups that synced from on-prem;
- ` az ad group list --query "[?onPremisesSecurityIdentifier!=null].displayName" `

All groups that are from Entra ID
- ` az ad group list --query "[?onPremisesSecurityIdentifier==null].displayName" `

Get Members of a group
- ` az ad group member list -g  "VM Admins" --query "[].[displayName] -o table`

Check if a user is a member of the specified group
- ` az ad group member check --group "VM Admins" --member-id <id> `

Get the object Ids of the groups of whichthe specified group is a member:
- ` az ad group get-member-groups -g "VM Admins" `

## AAD Apps
Get all application objects registered with the current tenant. An Applicaiton object is the global representation of an app.
- ` az ad app list `
- ` az ad app list --query "[].[displayName]" -o table`

Get all details about an application using identifier uri, application id or object id
- ` az ad app show -id <id> `

Get an applicaiton based on the display name(Run from cmd)
- ` az ad app list  --query "[?contains(displayName, 'app')].displayName" `

Search for apps that contain the word slack in their display name using PowerShell
- ` az ad app list | ConvertFrom-Json | %{$_.displayName -match "app"} `

Get owner of  an application
- ` az ad app owner list  -id <id> --query "[].[displayName]" -o table`

List apps that have password credentials
- ` az ad app list --query "[?passwordCredentials != null].displayName" `

List apps that have key credentials (use of certificate authentication)
- ` az ad app list --query "[?keyCredentials != null].displayName `

## AAD Service Principals
- Service Principals are visible as Enterprise applications in the Azure portal
- Service Principal is local representation for an app in that specific tenant and it's Security object as privileges.

Get all service principals:
- ` az ad sp list --all `
- `az ad sp list --all --query "[].[displayName]" -o table`

Get all details of a service principal based on the display name 
- ` az ad sp show --id <id> `

Get a service principal based on the display name
- ` az ad sp list --all --query "[?contains(displayname,'app')].displayName" `

Get Service principal based on display name using powershell
- ` az adap list --all | ConvertFrom-Json | %{$_.displayName -match "app"} `

Get owner of a service principal
- `az ad sp owner list --id <id> --query "[].[displayName]" -o table `

Get service principals owned by current user:
- ` az ad sp list --show-mine `

List apps that have password credentials: 
- `az ad sp list --all --query "[?passwordCredentials != null].displayName" `

List apps that have key creds 
- `az ad sp list --all --query "[?keyCredentials != null].displayName"
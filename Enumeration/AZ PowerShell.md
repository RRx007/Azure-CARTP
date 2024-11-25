- Az PowerShell is a module from Microsoft for managing Azure. 
- You can install by entering this command in PS: `Install-Module az`
- We must be making a list of interesting targets. 

To be able to use PowerShell Module we need to connect to Entra Id
- ` Connect-AzAccount `

You can also use PSCredential object with access tokens to connect to Entra
```powershell
$creds = Get-Credential
Connect-AzAccount -Credential $creds


$passwd = ConvertTo-SecureString "<Password>" -AsPlainText -Force
$creds = New-Object System.management.Automation.PsCredential("test@defcophq.onmicrosoft.com", $passwd)
Connect-AzAccount -Credential $creds
```

- Az PowerShell can enumerate both Entra Id and Azure Resources
All Entra Id cmdlets have the format *-AzAD*
- ` Get-Command *azad* `
- ` Get-AzADUser `

Cmdlets for other Azure resources have the format  *Az*
- ` Get-Command *az* `
- ` Get-AzResource `

Find cmdlets for a certain resource: 
```powershell
Get-Command *azvm*
Get-Command -Noun *vm* -Verb Get
Get-Command *vm*
``` 

Get information about current context(Account, Tenant, Subscription, etc)
- ` Get-AzContent `

List all available context:
- ` Get-AzContent -ListAvailable `

Enumerate subscription accessible by current user
- ` Get-AzSubscription `

Enumerate all resources visible to the current user
- ` Get-AzResource `

Enumerate all Azure RBAC role assignments
- ` Get-AzRoleAssignment `

## AAD Users(Entra ID)
Enumerate all users: 
- ` Get-AzADUser `

Enumerate a specific user
- ` Get-AzADUser -UserPrincipalName test@defcophq.onmicrosoft.com `

Search for a user based on the string in first character of DisplayName
- ` Get-AzADUser -SearchString "admin" `

Search for users who contain the word "admin" in their Display name
- ` Get-AzADUSer | ?{_.Displayname -match "admin"} `

## AAD Groups
List all Groups
- ` Get-AzADGroup `

Enumerate a specific group
- ` Get-AzADGroup -ObjectId <id>`

Search for a group based on the first character on the Display name
- ` Get-AzADGroup -SearchString "admin" |fl * `

Search for groups that have a certain word in their display name
- `Get-AzADGroup | ?{$_.Displayname -match "admin"}

Get Members of a group:
- ` Get-AzADGroupMember -ObjectId <id> `

## AAD Apps
- An Application object is the global representation of an app

Get All Application objects registered with the current tenant: 
- ` Get-AzADApplication `

Get All details about an application
- ` Get-AzADApplication -ObjectId <id> `

Get an application based on the display name
- ` Get-AzADApplication | ?{$_.Displayname -match "app"} `

List All Apps with an application password:
- `  Get-AzADApplication | %{if(Get-AzADAppCredential -ObjectId $_.ID){$_}}`
- the `Get-AzADAppCredential` will show the applications with an app password but the value isn't shown.


## AAD Service Principals
- Enumerate Service Principals (visible as Enterprise Applications in Azure Portal)
- Service Principal is a local representation for an app in a specific tenant and it's security object that has privileges (This is the Service Account)
- Service Principals can be assigned Azure roles

Get all Service Principals
- ` Get-AzADServicePrincipal `

Get all details about a service principal
- ` Get-AzADServicePrincipal -ObjectId <id> `

Get a service principal based on the display name
- ` Get-AzADServicePrincipal | ?{$_.Displayname -match "app"} `
## Az PowerShell
- Both Az PowerShell and AzureAD modules allow the use of access tokens for authentication. 
- Usually, tokens contain all claims so they are useful in bypassing such security controls. 

If you are already connected to a tenant, request an access token for resource manager(ARM)
- ` Get-AzAccessToken`
- ` (Get-AzAccessToken).Token `

Request an access token for Microsoft Graph to access Entra ID. (Supported tokens -AadGraph, AnalysisServices, ARM, Attestattion, Batch, Datalake, Keyvault, MSGraph, OperationInsights, Resource Manager, storage, synapse)
- ` Get-AzAccessToken -ResourceTypeName MSGraph `

From older versions of Az PowerShell, get a token for MS Graph
- ` (Get-AzAccessToken -Resource "https://graph.microsoft.com").Token `

Use the access token: 
- ` Connect-AzAccount -AccountId test@defcorphq.onmicrosoft.com -AccessToken <toke>`

Use other access tokens for accessing Entra Id
- `Connect-AzAccount -AccountId test@defcorphq.onmicrosoft.com -AccessToken <toke> -MicrosoftGraphAccessToken <toke> `

## AZ CLI
- `az cli` can request a token but cannot use it.

Request an access token (ARM)
- ` az account get-access-token `

Request an access token for aad-graph. Supported Tokens: aad-graph, arm, batch, data lake, media, ms-graph, oss-rdbms
- ` az account get-access-token --resource-type ms-graph `

## AzureAD Module
- AzureAD module cannot request a token but can use one for AADGraph or Microsoft Graph

Use the AAD Graph token
- ` Connect-AzureAD -AccountId test@defcorphq.onmicrosoft.com -AadAccessToken <token>`

Use the MS Graph token with Mg module
- ` Connect-MgGraph -AccessToken ($Token | ConvertTo-SecureString -AsPlaintext -Force) `

## Management
The 2 REST APIs endpoints that are most widely used are:
- Azure Resource Manager - managment.azure.com
- Microsoft Graph - graph.microsoft.com (AADGraph which is depreciated is graph.windows.net)
- we can use the below powershell script 

### ARM 
Get and Access token and use it with ARM API. For example, list all subscriptions.
```powershell
$Token = <token>
$URI = 'https://management.azure.com/subscriptions?api-version=2020-01-01'

$RequestParameters = @{
	Method = 'GET'
	URI = $URI
	Headers = @{
		'Authorization' = "Bearer $Token"
	}
}
(Invoke-RestMethod @RequestParameters).value
```

### Mg
Get an Access Token for Ms Graph. For example to list all users:
```powershell
$Token = <token>
$URI = 'https://graph.microsoft.com/v1.0/users'

$RequestParameters = @{
	Method = 'GET'
	URI = $URI
	Headers = @{
		'Authorization' = "Bearer $Token"
	}
}
(Invoke-RestMethod @RequestParameters).value
```

## Continuous Access Evaluation (CAE)
- CAE can help in invalidating access tokens before their expiry time(default 1 hour)
- Useful in cases like:
	- User Termination or password change enforced in near real time
	- Blocks use of access token outside trusted  locations when location based conditional access is present
- in CAE session, the access token lifetime is increased up to 28 hours.

When targeting an Azure enterprise environments to try and stay away from user identities(Don't try to compromise them). They are easier to protect and more prone to detection. Compared to workload identities that are often left as is once they are working which means there are less chances of detection or blocking access. 
### Claims Challenge
- CAE needs the client to be CAE-able -the client must understand that the has not been expired but can't be used 
- Outlook, Teams, Office and OneDrive web apps and apps support CAE
- `xms_cc` claim with a value of `cp1` in the access token is the authorive way to identify a client application is capable of handling a claims challenge.
- In case the client is not CAE-able , a normal access token with 1 hour expiry will be issued 
- We can finnd xms_cc claim in MSGraph for testuser that was requested using Az PowerShell
- Access Tokens issued for managed identities by Azure IMDS are not CAE-enabled

CAE works in 2 scenario:
- Critical Event Evaluation 
	- User account is deleted or disabled 
	- password change or reset for a user 
	- MFA enabled  for a user 
	- Refresh Token is revoked 
	- High Risk User detected by Entra Id Protection
	- Only Exchange Online and Sharepoint online and teams are supported. 
- Conditional Access Policy evaluation 
	- only ip-based named locations are supported
	- Only Exchange Online and Sharepoint online, Teams and MS Graph are supported. 

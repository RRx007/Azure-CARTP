
We only know the domain name or email addresses of the target organization
- defcorphq
We can extract some interesting information: 
- if the target organization uses azure tenant 
- Tenant ID
- Tenant Name
- Authentication Type (Federation or Not)
- Domains
- Azure Service used by target organization
- Guess Email IDs being used by the target organization. 
## Azure Tenant 
Get if azure Tenant is in use, you can confirm the Tenant Name and Federation with the below url: 
- ` https://login.microsoftonline.com/getuserrealm.srf?login=%5bUSERNAME@DOMAIN%5d&xml=1 `

To get the Tenant ID we can use this URL: 
- ` https://login.microsoftonline.com/%5bDOMAIN%5d/.well-known/openid-configuration `

We can validate user's Email ID by sending POST request to this URL:
- ` https://login.microsoftonline.com/common/GetCredentialType `

We can also use PowerShell Module AADInternal tool for recon: 
- ` https://github.com/Gerenios/AADInternals `
- we can run this tool by runnning powershell and following the below commands:
```
Install-Module AADInternals
Import Module https://github.com/Gerenios/AADInternals
AADInternals.psd1 -verbose
```

You can get the name, authentication, brand name and domain name 
`Get-AADIntLoginInformation -Username root@defcorphq.onmicrosoft.com`

Get Tenant ID: 
` Get-AADIntTenantID -Domain defcorphq.onmicrosoft.com `

Get Tenant Domains: 
` Get-AADIntTenantDomains -Domain defcorphq.onmicrosoft.com `
` Get-AADIntTenantDomains -Domain deffin.onmicrosoft.com `
` Get-AADIntTenantDomains -Domain microsoft.com `

Get all information ;
` Invoke-AADIntReconAsOutsider -DomainName defcorphq.onmicrosoft.com `


## Email ID
We can use o365creeper to check if an email ID belong to a tenant. It makes requests to the GetCredentailType API mentioned earlier. 
- ` https://github.com/LMGsec/o365creeper `
```
0365creeper.py -f emails.txt -o validemails.txt
```

## Azure Services
Azure Services are available at specific domains and subdomains. We can enumerate the target services by looking for such subdomains. 
- we can use this tools called MicroBurst that will help us with Azure services discovery. 
- ` https://github.com/NetSPI/MicroBurst `
- make sure to import the module to PS

You can enumerate all subdomains for an organization specified using the '-Base' parameter:
` Invoke-EnumerationAzureSubDomains -Base defcorphq -verbose `
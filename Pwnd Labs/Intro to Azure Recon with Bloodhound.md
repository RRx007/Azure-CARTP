These are the Azure creds given to us for the lab:

```

First we need to provide the Azure Tenant ID. We can do this using `az login` and the above creds. 
```powershell
$passwd = ConvertTo-SecureString "Bigt3ch123!" -AsPlainText -Force

$creds = new-Object System.Management.Automation.PSCredential("Jose.Rodriguez@megabigtech.com", $passwd)

Connect-AzAccount -Credential $creds

Import-Module AzureAD -UseWindowsPowerShell
Connect-AzureAD -Credential $creds


Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
Import-Module .\AzureHound.ps1 

Invoke-AzureHound -Verbose

# Linux
Bloodhound passwd: ym52zFBPJ5Via5LQyJfEtepgWhYqYSHX 
```

For PS scirpts that don't run cause of exectution policy
`Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass`

We can use this command to find the tenant ID:
`Connect-AzureAD -Credentials $creds`

If using PowerShell core need to use this command to connect AzureAD since that module is not support anymore for that version of PS:
` Import-Module AzureAD -UseWindowsPowerShell`


Need to enable Virtulization on Vmware to run wsl2


To run BloodHound we can either use the .exe file provided by the course with neo4j or the newer version of BH which is ran by using docker: 
```
docker compose pull && docker compose up
```

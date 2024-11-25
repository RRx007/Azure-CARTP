We can enumerate the application that has application proxy configures using the Azure AD module (may take a few minutes to complete)
- ` . C:\AZAD\Tools\GetMgApplicationProxyApplication.ps1`

Get the Service Principal (Enterprise Application)
- ` Get-MgServicePrincipal -Filter "DisplayName -eq 'Finance Management System`

Use `GetMgApplicationProxyApplication.ps1` to find users and groups assigned to the application
- ` . C:\AZAD\Tools\Get-MgApplicationProxyAssignedUsersAndGroups.ps1`
- ` Get-ApplicationProxyAssignedUsersAndGroups -ObjectId <id>`
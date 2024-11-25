# Hybrid Identity - On-Prem to Cloud
- It's recommended by Microsoft to join the Entra Connect server to the on-prem AD
- This means that the persistence mechanisms for on-prem (like Golden Ticket, Silver Ticket, ACL Backdoors and others) that provide us wither DA on the on-prem or local admin on the Entra Connect server will allow to get GA on Entra ID on demand.
	- for PHS, we can extract credentials 
	- for PTA, we can install the agent 
	- for Federation, we can extract the certificate from ADFS server using DA 

## Federation - Trusted Domain
- if we have GA privileges on a tenant, we can add a new domain(must be verified) configure its authentication type to Federated and configure the domain to trust a specific certificate (any.sts in the below command) and issuer. Using AADInternals 
` CovertTo-AADIntBackdoor -DomainName cyberranges.io`
- Get ImmutableID of the user that we want to impersonate. Using Msol module 
` Get-MsolUser | select userPrincipalNmae, ImmutableID`
- Access any cloud app as the user
- ` OpenAADIntOffice365Portal -ImmutableID <immuableid> -Issuer "http://any.sts/B231A11F" -UserBuiltInCertificate -ByPassMFA `

` https://aadinternalsbackdoor.azurewebsites.net/`

### Token Signing Certificate 
- with DA privileges on on-prem AD, it is possible to create and import new token signing and Token Decryption certificates that have a very long validity
- this will allow us to log-in as any user whose ImmutableID we know
- Run the below command as DA on the ADFS server to create new certs (default password: 'AADInternals'), add them to ADFS, disable auto rollover and restart the service.
` New-AADIntADFSSelfSignedCertificates`
- Update the certificate information in Entra ID
` Update-AADIntADFSFederationSettings -Domain cyberranges.io`

# Storage Account Keys
- we already know that keys provide root equivalent privileges on a storage account 
- there are 2 access keys and they are not rotated automatically(unless a key vault is managing keys)
- this of course provides neat persistent access to the storage account
- we can generate SAS URL (including offline minting) using the access keys

# Applications and Service Principals
- Entra Enterprise Application(service Principals) and App Registration (applications) can be used for persistence
- with privileges of Application Administrator, GA or a custome role with `microsoft.directory/applications/credentials/update` permissions, we can add credentials (secret or certificate) to an existing application
- by targeting an application with high permissions this is a very useful persistence mechanism
- it also bypasses MFA
- we can also add a new application that has high permissions and then use that for persistence.
- If we have GA privileges, we can create an application with the Privileged authentication administrator role - that allows to reset password for Global Administrators. 
- Sign in as a service principal using Az Powershell(use the application ID as the username, and the secret as passsword)
```powershell
$passwd = ConvertTo-SecureString "J~Q~QMt_qe4uDzg53MDD_jrj_Q3P.changed" -AsPlainText -Force
$creds = New-Object System.Management.Automation.PSCredential("311bf843-cc8b-459c-be24-6ed908458623", $passwd)

Connect-AzAccount -ServicePrincipal -Credential $creds -Tenant <tenantID>
```

- for certificate based authentication 
` Connect-AzAccount -ServicePrinicpal -Tenant <tenantID> -CertificateThumbprint <thumbprint> -ApplicationID <applicationID>`
- we can use az cli too to sign in as a service principal

# Illicit Consent Grant
- by default an user can register an application in Entra ID
- we can register an application (only for the target tenant) that needs high impact permissions with admin consent - like sending mail on a user's behalf, role management etc.
- This will allow us to execute phishing attacks that would be very fruitful in case of success.

# Azure VMs and NSGs 
- OS level persistence on an Azure VM where we have remote accessis very useful 
- Azure Vms also support managed identity so persistence on any such VM will allos us access to additional Azure resources
- we can also create snapshots of disk attached to a running VM. This can be used to extract secrets stored on disk (like SAM hive for Windows)
- its also possible to attach a modified/tampered disk to a turned-off VM. for example, add a local administrator.
- Couple this with modification of NSGs to allow access from IPs that we control. 

# Custom Entra ID Roles
- if we have GA in a tenant, we can modify a custom role and assign that to a user that we control. 
- take a look at the permissions of the built-in administrative roles, we can pick individual actions. it is always helpful to go for minimal privileges 
- for example, actions allowed to Application Developer are good enough for a low-privilege persistence as they allow application registration even it - "Users can register applications" setting is set to NO

# Deployment Modification
- recall the github account that we compromised
- if we have persistent access to external resources like Github repos that are apart of deployment chain it will be possible to persist in the target
- Often a Github account would not have same level of security and monitoring compared to an Entra ID account with similar privileges 
- this is just an example, deployment modification has a huge attack surface. 

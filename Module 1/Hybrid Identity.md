- organizations have resource, devices and applications both on-premises and in the cloud
- Many enterprises use their on-prem AD identities to access Azure applications to avoid managing separate identities on both
- "A signle user identity for authentication and authorization to all resource, regardless of location is hybrid identity"
` https://learn.microsoft.com/en-us/entra/identity/hybrid/whatis-hybrid-identity`

# Entra Connect
- An on-prem AD can be integrated with Entra ID using Entra Connect with the following methods. Every method supports Single Sign-On(SSO)
	- Password Hash Sync (PHS)
	- Pass-Through Authentication
	- Federation
- for each method, at least the user synchronization is done and an account `MSOL_<installationIdentifier>` is created on the on-prem AD.

# Cloud Sync
- Cloud Sync can also be used for Hybrid Identity
- it uses a 'lightweight' agent in place of the Entra Connect Application
- Cloud Sync doesn't support PTA
- Comparison between Entra Connect and Cloud Sync
`https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/what-is-cloud-sync#comparison-between-microsoft-entra-connect-and-cloud-sync`

# Password Hash Sync(PHS) 
- it syncs users and a hash of their password hashes (not clear-text or original hashes) from on-prem to Entra ID
- the simplest and most popular method for getting a hybrid identity
- PHS is required for features like Identity Protection and AAD Domain Services
- Hash synchronization takes place every 2 minutes
- When a user tries to access any Azure resource, the authentication takes place on Entra ID
- built-in security groups are not synced
- by default, password expiry and account expiry are not reflected in Entra ID. That means a user whose on-prem password is expired can continue to access Azure resources using the old password

# Seamless SSO
- Entra Seamless SSO automatically signs users in when they are on on-prem domain-joined machine. There is no need to use passwords to log in to Entra ID and on-prem applications 
- supported by both PHS and PTA 
- When seamless SSO is enabled a computer account AZUREADSSOACC is created in the on-prem AD. This account's Kerberos decryption key is shared with Entra ID
- Entra ID exposes an endpoint (`https://autologon.microsoftazuread-sso.com`) that accepts Kerberos Tickets. Domain-joined machines's browser forwards the ticket to this endpoint for SSO.

### AZUREADSSOSCC - On-Prem to Cloud
- password/key of the AZUREADSSOACC never changes 
- if we can compromise the NTLM hash of the AZUREADSSOACC machine account, we can create Silver Ticket for any synced on-prem user
` Invoke-Mimikatz -Command '"lasdump::dcsync /user:defeng\azureasssoacc$ /domain:defeng.corp /dc:defeng-dc.defeng.corp `
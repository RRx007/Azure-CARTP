# Authentication and API's
- Microsoft's identity platform uses OpenID Connect (OIDC) for authentication and OAuth 2.0 for authorization.
- Entra ID supports multiple types of authentication like SAML 2.0, OIDC, OAuth 2.0 and Legacy authentication protocols for synchronization like header based, LDAP, Kerberos Constrained Delegation, etc.
- ` https://learn.microsoft.com/en-us/entra/identity-platform/v2-protocols `
- An application can sign into the Authorization server and get bearer tokens to access Resource server. 
![[Pasted image 20241116074425.png]]
## Basic Sign-In

![[Pasted image 20241116074453.png]]

### Basic Sign in with Access Tokens acquisition 
![[Pasted image 20241117015702.png]]
- in this case the web server/web app needs to access a Web Api on behalf of the users.
- this is achieved by acquisition of tokens using Oauth authorization code flow. 


## Tokens
- OAuth 2.0 and OIDC use bearer tokens which are JSON Web tokens
- A bearer token, as the name suggests, grants the bearer access to a protected resource.

There are 3 types of tokens used by OIDC:
- `Access Tokens` - the client presents this token to the resource server to access resources. It can be used only for a specific combination of users, client, resource and can;t be revoked until expiry
- `ID Tokens` - the client receives this token from the authorization serve. It contains basic information about the user. it is bound to a specific combination of user and client 
- `Refresh Tokens` - Provided to the client with access. Used to grant new access and ID tokens It is bound to a specific combination of user and client  and can be revoked. Default expiry is 90 days for inactive refresh tokens and no expiry for active tokens. 






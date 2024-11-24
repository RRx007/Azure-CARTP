- Application Proxy allows access to on-prem web applications after sign-in to Entra ID

Application Proxy has the following components:
- Endpoint - this is the external URL that the users browse to access the op-prem application. External users must authenticate to AAD
- Application Proxy Service - this service runs in the cloud and passes the token provided by Entra ID to the on-prem connector.
- Application Proxy Connector - this is an agent that runs on the on-prem infrastructure and acts as a communication agent between the on-prem AD in case of SSO
- On-prem Application - the application that is exposed using application proxy
` https://learn.microsoft.com/en-us/entra/identity/app-proxy/overview-what-is-app-proxy`
![[Pasted image 20241123051212.png]]

# Abuse
- compared to directly exposing an on-prem app, application proxy does provide additional security (authentication is handled by Entra ID, Conditional Access etc)
- But is does not help if the on-prem application has code or deployment related vulnerabilities 
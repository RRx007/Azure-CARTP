Any application registered in Entra Id has 2 representations.
- Application - object that is present only in the tenant where app is registered. this is visible under app registrations in the Azure Portal
- Service Principal - that is present in every directory where application is used (in case of a multi-tenant environment) this is visible under Enterprise Applications in the Azure Portal. Azure RBAC roles use Service principal
- An application has one application object in its home directory that is referenced by one or more service principals in each of the directories where it operates (including the application's home directory)
- Service Principal (Enterprise Application) are instances of an Application. 

## Clients Secrets
- an application object supports multiple secrets(application passwords)
- a user that is owner or have application administrator role over an application can add an application password. 
- An application password can be used to login to a tenant as a service principal. MFA is usually not applied on a service principal.

# Abuse
If we compromise a user that has enough permissions to create a client secret/application password for an application object, we can:
- Login as the service principal for that application 
- Bypass MFA
- Access all the resources where roles are assigned to the service principal 
- Add credentials to an enterprise application for persistence after compromising a tenant. 
  
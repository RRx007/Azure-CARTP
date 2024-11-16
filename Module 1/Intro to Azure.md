Azure is Microsoft's Cloud Computing Platform with over 200 products and cloud services. MS claims that 95% of Fortune 500 companies use Azure for cloud services. 

## Azure Services
- **Compute** - VM's, Kubernetes, Containers, Function Apps
- **Networking** - VNET, VPN Gateway, Load Balancer, CDN
- **Storage** - Blob, File, Queue, Table
- **Mobile** - Back-end services
- Databases - Cosmos DB, Managed SQL, MYSQL, PostGresSQL
- Web - App Services, API Management, Cognitive Search 
- Internet of Things
- Big data 
- AI
- DevOps

## Entra ID 
- Entra ID is MS Cloud-based identity and access management service. 
- Can be used to access both 
	- External resources like Azure Portal, Office 365
	- Internal resources like on-premises applications 
- Provides secure remote access for AD-integrated apps, devices and identity governance for AD accounts. 
![[Pasted image 20241114025532.png]]

### Entra ID vs Azure
- Entra is not Azure but a product offering within Azure. 
- Azure is MS Cloud platform whereas Entra ID is Enterprise identity service in Azure. 

### Entra ID Terminology: 
- **Tenant** - an instance of Entra ID and represents a single organization
- **Entra ID Directory** - each tenant has a dedicated Directory to perform identity and access management functions for resources
- **Subscriptions** - used to pay for service and can have multiple subscription in a directory 
- **Core Domain** - the initial domain name `<tenant>.onmicrosoft.com`. You can also set custom domains. 

## Azure Architecture
In Azure, resources are divided in four levels: 
- Management Groups
- Subscription
- Resource Groups 
- Resources

#### Management Groups: 
- are used to manage multiple subscriptions
- all subscriptions inherit the conditions applied to the management group
- subscriptions within a single management group belongs to the same Azure tenant
- management groups can be placed in a lower hierarchy of another management group 
- there is a single top-level management group -> Root Management Group for each directory in azure. 
- Global admin can elevate their privileges to the root management group

#### Subscriptions
- an azure subscription is a logical unit of azure services that links to an azure account.
- An Azure subscription is a billing /access control boundary in an Entra ID directory. 
- An Entra ID Directory may have multiple subscriptions but they can only trust a single directory 
- Azure role applied at the subscription level applies to all resources within the subscription.

#### Resource Groups and Resources: 
- a resource is a deployable item in Azure. 
- a resource group acts as a container for resources
- all resources must be inside a resource group and can belong to any group
- if a resource group is deleted then all resources are in that group are deleted aswell

#### Managed Identity
- azure provides the ability to assign Managed identities to resources like app service, function apps, virtual machines.
- Managed Identity uses Entra ID tokens to access other resources that support Entra ID authentication.
- It is a Service principal of special type that can be used in Azure resources
- Managed Identity can be system-assigned or user-assigned.

#### Azure Resource Manager
- it's a client neutral deployment and management service for azure that is use for lifecycle management and access control of resources. 
- ARM templates can be used for consistent and dependency-defined redeployments of resources. 

#### Entra ID vs On-Prem AD
- both are identity and access management solutions. 
- Entra ID is not directory services in the cloud that is Azure Active Directory Domain Services which can provide Domain Controller as a Service. 
- you can integrate both on-prem AD and Entra ID for a Hybrid Identity.


#### Azure RBAC Roles
- provides access management for azure resources using the authorization system of ARM
- there are over 120 built-in roles and we can define custom roles. we can view them all in this link: `https://azure.permissions.cloud/builtinroles`
- There are five fundamental Azure Roles:
![[Pasted image 20241114044118.png]]

#### Azure RBAC Assignment 
- Security Principal - an Entra ID user, group, service principal or managed identity
- Role Definition - a collection of permissions that lists the actions a security principal can perform 
- Scope - the resource where the role is applied. Scope Levels:
	- Management group
	- Subscription
	- Resource group
	- Resource
![[Pasted image 20241114045153.png]]

- role assignment is transitive for groups
- for multiple roles assignments the permissions are sum of all the role assignments
- explicit deny role assignments takes precedence. 
 ![[Pasted image 20241114045555.png]]

#### Azure ABAC (Attribute-Based Access Control)
- ABAC builds on RBAC and provides fine-grained access control based on attributes of a resource, security principal,  and environment 
- implemented using role assignment condition
![[Pasted image 20241114045947.png]]

#### Entra ID Roles: 
- Entra ID roles are applicable on Entra ID resources like user, groups, domains, licenses
- there are many Administrator roles in Entra ID, you can also define custom roles. 
- Global Administrator is the most well-known and all powerful admin role.
- Global Admin has the ability to elevate to User Access Administrator Azure role to the root management group 

#### Entra Editions 
- Free 
	- core identity and access features 
	- included with azure, MS 365
- P1
	- Free plus advanced features for password and group access management, conditional access and hybrid identity. 
- P2
	- P1 capabilities with risk based conditional access, identity protection and governace features

- In any enterprise environment, one of the most important aspects of security is visibility
- we can't protect what we can see
- Azure provides visibility of Entra ID and Azure resources to apply security controls on them. 
- However, this is usually comes with considerable additional costs.

### Guidance:
- Microsoft Security Best Practices: `https://learn.microsoft.com/en-us/security/compass/compass`
- Azure Security Fundamentals: ` https://learn.microsoft.com/en-us/azure/security/fundamentals/overview`
- Microsoft Cloud Security Benchmark: ` https://learn.microsoft.com/en-us/security/benchmark/azure/`

There are 6 Functional areas for built-in Azure security capabilities:
- Operations
- Identity
- Applications
- Storage
- Networking
- Compute

## Operations
- Microsoft Defender for Cloud - unified infrastructure security management  
- Azure Resource Manger - allows secure, repeatable template-based deployment
- Application Insights - Analytics for applications
- Azure Monitor - monitor on-prem and other cloud logs 
- Azure Advisor - provides security, performance and reliablility recommendations

### Microsoft dDefender for Cloud
- provides a unified security management
- Azure resources are automatically provisioned in the security center. on-prem servers need to be on-boarded using log analytics agents 
- It provides
	- Resource security hygiene using secure score
	- Automatic recommendation to improve security posture
	- Asset inventory
- it has no additional cost and provides what Microsoft calls Cloud Security Posture Management(CSPM)
#### Secure Score 
- secure score reviews security recommendations and provides an overall measure of the security posture. 
- secure score by category shows the resources which need most attention
- You can improve your secure score by addressing all the recommendations in a control in the Recommendations section.

#### Workload Protection
- is a pay-per resource part of Microsoft Defender for Cloud
- Provides the Cloud Workload Protection (CWP) for Azure resources. 
	- VMs - Defender for endpoint, Vulnerability scanning, Just-in-time access for management ports, File integrity monitoring, Adaptive application controls
	- App Service - Web App vulnerabilities detection, Monitoring of the VM where app service is running(consumption plan is not supported)
	- Storage - Suspicious Access and Activites, upload of malicious content
	- Key Vault- Suspicious Access and activites
	- Resource Manger - Suspicious access, lateral movement for management plan to data plane

#### Adaptive Application Controls
- allows enforcing application allow list (allow only specific applications to run) on Azure and non-Azure machines
- only audit mode is supported 
- theis is a learning period of 2 weeks before recommendations are shown for a VM
- It uses Applocker on windows machines to enforce the recommendations are made.

#### Just-in-time(JIT) VM Access
- allows securing the management ports of a VM against brute-force attacks and unauthorized network access.
- this protection is achieved by blocking the incoming traffic on the management ports for a configured VM and allowing access on per request basis for a limited time and from limited IP or network range. 
- When a user requests access to a VM, security center checks the roles(RBAC) assigned to the user in subscription or relevant resource group
- if the user has the required permissions, NSGs(and Azure firewall, if in use) are automatically modified to allow access.
- By-default, 4 management ports(22, 3389,5985, 5986) are protected but more can be added
- A user need the following role assignments on the subscription or the resource group associated with the VM to be able to request access:
	- `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action
	- `Microsoft.Security/locations/jitNetworkAccessPolicies/read
	- `Microsoft.Compute/virtualMachines/read
	- `Microsoft.Network/networkInterfaces/*/read
- A user with these role assignments can go to the `Connect` blade of the virtual machine and request access
- Once the rquest is approved a time-bound allowed rule is added to the NSG (and Azure Firewall if in use) to allow the inbound access

#### File Integrity Monitoring 
- examines files, registries, and applications for changes to detect attacks
- it checks if the current checksum of a file is different from the last scan 
- FIM supports windows and linux and by default monitors well known files and registry keys according to known attack patterns
- Once FIM is enabled it's possible to add custom Registry Keys, Windows files and Linux files and file content in the monitor list 

#### Adaptive Network Hardening (ANH
- helps further hardening the netowrk security groups (NSG)
- if a NSG allows traffic from a fixed source subnet (say, 132.21.45.61/24) adaptive network hardening learns where the actual traffic comes from and reccomended using the smaller set of IP address which should be allowed to access the VMs
- only VMs deployed through Azure resource manager are supported and 30 days of traffic is required for learning 
- this feature supports only limited set if ports.

### Monitor 
- Azure Monitor collects data from Azure subscriptions and resources
- we can see the logs in individual resources under the Monitoring Tab
- Useful for monitoring, querying and abalysing logs from multiple source (Applications, OS, Tenant, Subscriptions, Resources)at one place
- Queries are written in the Kusto Query Language(KQL)

### Identity and Access Management
- Secure Identity
	- MFA
	- Microsoft Authenticator 
	- Password policy enforcement 
	- Token-based authentication
	- Azure RBAC
	- Hybrid Identity
- Secure Apps and data using Entra ID
	- Cloud Apps Discovery
	- Identity Protection
	- Azure Active Directory Domain Services
	- Application Proxy

#### MFA
- uses 2 or more of the options below for authentication 
	- What do you know (Password)
	- What do you have (OTP, Temporary PIN, Compliant Device)
	- Who you are(biometrics)
- it's recommended to enable MFA for all users in Entra ID. 
- Following options are available for enabling MFA:
	- Security Defaults (All USers must start using it with 14 days of first sign-in)
	- By changing user state (Users blade in EntraID)
	- Using Conditional Access Policy for more granular control
		- with user risk and sign-in risk input from Azure Identity Protection
		- with conditions like sign-in locations, device trusts etc

#### Security Defaults
- there used to be  baseline protection policies . Instead of that we ca use Security Defaults for some pre-configured policies which are useful for most organizations 
- Its avaliable under Properties blade of an Entra ID as "Manage Security Defaults"
- if Conditional access polices are defined, we can't use Security Defaults
- Available without additional licensing 
- Following policies are pushed by Security Defaults
	- All users in the tenant must register for MFA within 14days of their first sign-in
	- Some Administrators roles always need MFA
	- Privileged actions for Azure Resource Manager using Azure portal, PowerShell or CLI needs additional authentication
	- Blocking Legacy Authentication(Used by older office clients and mail protocols)

#### Conditional Access
- uses Signals to make Decisions and Enforce Policies
- Conditional Access policies at their simplest are if-then statements, if a user wants to access a resource, then they must perform MFA
- allows automated access control decisions based on pre-defined conditions
- when creating a policy we can set it Report-only which is an audit more(logs visible in Directory Sign-ins)
- Policies are enforced post-authentication 
- Azure Premium P1 license is required
![[Pasted image 20241124072431.png]]

##### Policy - Assignments
- The Assignment portion covers the signals required for a policy
	- users, groups or roles who the policy includes or excludes
	- Cloud apps or other related apps
	- Conditions
		- User Risk
		- Sign-in
		- Device Platforms
		- Location
		- Client app -browser, mobile, device state -(managed or unmanaged)
		- Filter for devices(based on device properties)
##### Access Controls
- the access controls portion controls how policy is enforced
- Grant ( For Blocking or Granting access)
	- Block Access
	- Grant Access
		- requires MFA
		- requires authentication strength for MFA
		- Require device to be marked compliant
		- Require Hybrid Entra joined device
		- require approved client app - an access attempt to the selected cloud apps to made from an approved client app
		- require app protection policy - Intune app protection policy must be present on the client app before access is available to the selected cloud apps
		- require password change

##### Session Control
- Session control is used to limit the experience
	- App enforced restrictions - Currently only supported apps are exchange online, Office 365 and Sharepoint online. 
	- Condition access app control - uses Defender for cloud apps to enforce:
		- Monitor Only
		- Block downloads
		- Custom Policies from MCAS are also supported
	- Sign-in frequency
	- Persistent browser session
	- customize continuous access evaluation
	- Disable resilience defaults

### Privileged Identity Management (PIM)
- using PIM we can control access to resource in AAD, Azure resources and Office365
- PIM requires EntraID premium P2 license or Enterprise Mobility + Security E5 or Microsoft 365 M5
- Compromise of high privilege users is a contiuous risk. we can use PIM to:
	- List administrator
	- Just-in-time and time bound administrative access 
	- Force multifactor authentication
	- Approval workflows for high privileges to Entra ID, Privileged Access groups and Azure resources
	- Access reviews
	- Reporting and Alerting for privileges roles
##### Alerts 
- for Azure resource managed using PIM they have 2 Alerts
- if an alert is active wen can look at is details by clicking it 
![[Pasted image 20241124082820.png]]


#### Entra ID Roles:
- In PIM a user may have assignment type eligible or active for a role 
	- Eligible means user can activate the role for performing privileged tasks. it needs a user to perform some actions to use the role
	- Active means a user can use the role without doing anything else
	- this is how PIM works for users eligible for privileged roles:
		- when an eligible users want to use their privileged role, they can activate the role, they can activate the role just-in-time
		- For activating the role they complete predefined steps like MFA, approval specifying a reason etc 
		- After completing the steps the user gets the role for a limited preconfigured time(time-bound)
		- Audit logs(resource audit) captures all activities
###### Alerts 
- there are 7 built-in alerts to spot common misconfiguration of roles 
- all alerts are enabled by-default and can be disabled individually
- if an alert is active, we can look at its details by clicking on it 
![[Pasted image 20241124082539.png]]

###### Access Review
- we can run access reviews on Entra ID roles to make sure that continued access rests with only the correct set of people 
- we ca create access review from the access reviews blade of PIM
- Access reviews can be on time or continuous

#### Entra Identity Protection
- provides risk detection and automated actions for securing identities
- Full features of Entra Identity Protection needs Entra Premium P@
- There are 2 types of risks:
	- User risks 
	- Sign-in risks
- For each of the above there are 2 types of detections
	- Real-time(takes 5-10 minutes)
	- Offline(takes 2-24 hours)
- The user and sign-in risks can be used with conditional access policies too
- Measures the security posture using identity secure score (updated every 48 hours)
#### User Risks
![[Pasted image 20241124235343.png]]

![[Pasted image 20241124235402.png]]

![[Pasted image 20241124235424.png]]

#### Report
- We can check the Report blade for various detections and summary of individual detection. Details for individual entires can be seen by selecting them
- Reports are available for 
	- Risky Users
	- Risky Workload identities
	- Risky sign-ins
	- Risky detections

#### Protect 
- we can define Policies under the Protect blade to create automated actions and remediation
- there are three default policies to select from with possibility of excluding users 
- for both the User risk and sign-in policies, its possible to:
	- Apply policy on all users or specific users and groups
	- Set condition based on risk severity
	- Configure control to be enforced - Block access, Allow access or Allow access with password change
# Microsoft 365 Defender 
- is a unified pre and post breach enterprise defense suite that natively coordinates detection, protection, investigation and response across endpoints, identities, email and application to provide integrated protection against sophisticated attacks 
- available at `https://security.microsoft.com`
- provides a unified portal for Defender for Endpoint, Defender for Office 365, Defender for Identity, Entra Identity protection and Microsoft Cloud App security 
- measures security posture using Microsoft Secure Score. 

# Microsoft Sentinel
- cloud-native security information event management (SIEM) and security orchestration automated response (SOAR)
- Sentinel can collect data from multiple sources including non-microsoft resources using connectors
- Data monitoring is done using workbooks 
- additional capabilities include 
	- Analytics - Used to correlate alerts to incidents 
	- Automation of common tasks, for example, creation of tickets
	- Hunting - Using built-in or custom queries hunt for possible threats 
	- Investigate incidents 
	- Entity Behavior
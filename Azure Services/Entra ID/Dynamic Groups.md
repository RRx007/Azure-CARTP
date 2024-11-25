- we can create rules -based on user or device properties - to automatically join them to a Dynamic group
- For example, an organization may add users to a particular group based on thier userPrinicipalName, department, mail, etc
- When a group membership rule is applied, all users and device attributes are evaluated for matches. 
- when an attribute changes for a user or device, all dynamic group rules are checked for a match and possible membership changes
- No Entra ID roles can be assigned to a Dynamic Group but Azure RBAC roles can be assigned. 
- Dynamic Groups requires Entra ID premium P1 license
`https://learn.microsoft.com/en-us/entra/identity/users/groups-create-rule`

# Abuse 
- by default, any user can invite guests in Entra ID
- if a dynamic group rule allows adding users based on the attributes that a guest user can modify, it will result in abuse of this feature. 
- there are 2 ways the rules can be abused
	- Before joining a tenant as guest. If we enumerate that a property, say mail, is used in a rule, we can invite a guest with the email ID that matches the rule. 
	- After joining a tenant as a guset. a guest user can 'manage their own profile', that is, they can modify manager and alternate email. we can abuse a rule that matches on Manager (Direct Reports for "{objectID_of_manager}") or alternative email (user.otherMails -any(_-contains "string"))
`https://learn.microsoft.com/en-us/entra/identity/users/groups-dynamic-membership#create-a-direct-reports-rule`


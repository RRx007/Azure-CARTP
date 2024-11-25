- applications can ask users for permissions to access their data. 
- If allowed a normal user can grant consent only for "Low Impact" permissions. in all other cases admin consent is required. 
- GA, Application Adminsitrator, Cloud Application Adminsitrator and a custom role including `permission to grant permissions to applications` can provide tenant wide consent. 

# App Consent Policy's
- Consent policies can be set for all users 
	- Do not allow user consent
	- Allow user consent apps from verified publishers, for selected permissions. Only for "Low Impact" permissions for apps from same tenant and verified publisher. 
	- Custom app Policy
- 'Allow user consent for all apps'  is interesting and abusable. 

## Low Impact Permissions 
- Only the permissions that don't need admin consent can be classified as low impact. 
- Permissions required fro basic sign-in are openid, profile, email, User.Read and offline_access.
- This means if an organization allows a user consent for all apps an employee can grant consent to an app to read the above from their profile. 
- there are some very interesting low impact permissions. For example User.ReadBasic.All allows the app to read displayname, first and last name, email address, openextensions and photo for all users

`https://graphpermissions.merill.net/permission/`
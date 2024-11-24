- scripts or any other data that can be inserted on a Azure VM at the time of provision or later.
- Any application on the virtual machine can access the user data from the Azure Instance Metadata Service (IMDS) after provision. 
- User data is:
	- persistent across reboots 
	- can be retrieved and updated without affecting the VM 
	- Not encrypted and any process on the VM can access the data
	- Should be base64 encoded and cannot be more than 64kb

# Abuse 
- Despite clear warning in the documentation, a lot of sensitive information can be found in user data
- Examples are, Powershell scripts for domain join operations, post-provisioning configuration and management, on-boarding agents, scripts used by infrastructure automation tools, etc.
- It is also possible to modify user data with permissions `Microsoft.Compute/virtualMachines/write` on the target VM. Any automation or scheduled task reading commands from user data can be abused 
- Modification of user data shows up in VM Activity Logs but doesn't show what changes where made.

Retrieve User data:
```powershell
$userData = Invoke-RestMethod -Headers @{"Metadata" = "true"} -Method GET -Uri "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text"

```

Modify User data: 
```powershell
$data = [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes("whoami"))
$accessToken =(Get-AzAccessToken).Token
$url = "https://management.azure.com/subscriptions/<subID>/resourceGroups/RESEARCH/providers/Microsoft.Compute/virtualMachines/jumpvm?api-version=2021-07-01"
$body = @{
 @{
	 location = "Germany West Central"
	 properties = @{
		 userData = "$data"
	 }
 }
} | Convert-Json -Depth 4

$headers = @{
	Authorization = "Bearer $accessToken"
}
#execute Rest API call
	$Results = Invoke-RestMethod -Method Put -Uri $url -Body $body -Headers $headers -ContentType 'application/json'
```


## Custom Script Extension
- extensions are "small applications" used to provide post deployment configuration and other management tasks.
- Custom Scripts Extension is used to run scripts on Azure VMs
- Scripts can be inline, fetched from blob storage (needs managed identity) or can be downloaded. 
- the script is executed with SYSTEM privileges
- can be deployed to a running VM
- only one extension can be added to a VM at a time. So it is not possible to add multiple custom script extensions to a single VM

# Abuse
- following permissions are required to create a custom script extension and read the output:
	- `Microsoft.Compute/virtualMachines/extensions/write`
	- `Microsoft.Compute/virtualMachines/extensions/read
- the execution of script takes almost immediately. 

# Entra ID Devices
- Microsoft Entra join 
	- Organization owned devices and heavily managed using Intune or Configuration Manager
	- Only windows 11 and 10 and Server 2019 machines running on Azure. 
	- can be accessed using Azure AD account
- Microsoft Entra registration
	- can be user owned (BYOD) or organization owned. Lightly managed
	- Windows 10 or newer. MacOS, ubuntu and mobile devices. 
- Microsoft Entra Hybrid Join
	- Organization owned devices joined to on-prem AD and registered with Entra ID 
	- All supported windows desktop and server versions

# Entra joined Machines
- when a machine is joined to Entra ID, following users/roles are made a member of the local administrator group for management. 
	- Global Administrator
	- Microsoft Entra Joined Device Local Administrator
	- user who joined the machine to Azure 
- Other Azure users can also be joined to local administrators group of Entra joined machines. 


# [[Primary Refresh Token]]
- if we compromise an Entra joined (or Hybrid) machine, it is possible to extract PRT and other keys for a user.
- For Entra registered machine, PRT is issued if a user has added a secondary work account. 
` https://dirkjanm.io/assets/raw/romhack_dirkjan.pdf `
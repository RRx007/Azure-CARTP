## Password Spray/Brute-Force
- we will use a single password against multiple users that we have enumerated.
- noisy and may lead to detection 
- In Azure password spraying attack can be done against different  API endpoints like: Azure AD Graph, Microsoft Graph, Office 365 Reporting, web service. 
- Have one user name and multiple passwords or vice vera multiple users one password 

We can use MSOLSpray for password spray against the accounts that we discovered:
- ` https://github.com/dafthack/MSOLSpray `

This tool also supports fireprox to rotate source IP address on auth request: 
```powershell
MSOLSpray.ps1
Invoke-MSOLSpray -UserList validemails.txt -Password testpass -Verbose
```
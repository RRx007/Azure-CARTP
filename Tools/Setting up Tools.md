This is a guide to setup the tools needed for the CARTP course.

Tools needed: 
- Az CLI:
- Az PowerShell
- MS Graph module (Mg module)
- Linux machines need open source powershell, .Net, C++ tools 

### AADInternals 
- is a Powershell module containing tools for administering and hacking Entra ID and Office365
```
Install-Module AADInternals

# to run it you need to import into the session
Import-Module AADInternals
```

## O365Creeper
- this is a python script that will query the Micrsoft 365 web server to determine if an account is valid or not.
- `https://github.com/LMGsec/o365creeper/blob/master/o365creeper.py`

## MicroBurst 
- `https://github.com/NetSPI/MicroBurst`
	- is a useful tool for security assessments of Azure 

## MSOLSpray
- password spraying tool for Microsoft Online accounts (Azure/O365)
`https://github.com/dafthack/MSOLSpray`

## Fireprox
- allows you to rotate the source ip of an auth request. 
- `https://github.com/ustayready/fireprox`
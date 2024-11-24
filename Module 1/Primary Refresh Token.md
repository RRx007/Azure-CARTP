- Recall that refresh tokens can be used to request new access tokens for a particular application. 
- PRT is a special refresh token used for a single sign-on (SSO)
	- it can be used to obtain access and refresh tokens to any application 
	- issued to a user for a specific device
	- Valid for 90 days and is continuously renewed
	- CloudAP SSP requests and caches PRT on device
	- If PRT is MFA-based (Windows Hello or Windows Account manager), then the claim is transferred to app tokens to prevent MFA challenge for every application
	- Before a fix in August 2021, PRT always had MFA claims. 


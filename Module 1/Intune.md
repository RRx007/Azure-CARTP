- Intune is a Mobile Device Management (MDM) and Mobile Application Management (MAM) service
- Intune needs an Enterprise Mobility +Security E5 license
- For devices to be fully managed using Intune, they need to be enrolled
- Enrolled Devices allow (IsCompliant or Compliant set to Yse in Azure Portal)
	- Access control using Conditional Access Policies 
	- Control installed applications, access to information, setup threat protection agents etc.

# Cloud to On-Prem
- using the Endpoint Manager at `https://endpoint.microsoft.com` , a user with Global Administrator or Intune Administrator role can execute powershell scripts on an enrolled Windows device. 
- The script run with privileges of SYSTEM on the device. We don not ge to see the script output and the script doesn't run again if there is no change
- as per documentation, the script execution takes place every hour but in my experience that is random
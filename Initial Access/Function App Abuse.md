- Azure Functions is Azure serverless solution to run code.
- languages like C#, Java, Powershell and python are supported.
- A function app is supposed to be used to react to an event like:
	- HTTP Trigger
	- Processing a file upload
	- Run code on a scheduled time and more
- App Service provides the hosting infrastructure for function apps
- function apps support managed identity

# Continuous Deployment
- Function apps support continuous deployments 
- If continuous deployment is being used, a source code update triggers deployment to Azure.
- Following Source code locations are supported:
	- Azure Repos 
	- Github
	- Bitbucket
- Deployment slots are supported so that deployments are first done in slots like staging (to avoid deploying directly into production)
- a misconfigured function app that deploys directly in production can be abused
- if the source/code of the function app location is compromised, it will be possiable to assume the identity of the function app. 
- For example, if Github is used as the provider and a comprise of the github account can lead to compromise of the function app

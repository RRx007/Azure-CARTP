- Infrastructure as a code service for Azure to deploy resources using code. 
- ARM templates are JSON files containing deployment configuration for Azure resources 
- ARM templates also support a language called Bicep.

## History
- each resource group maintains a deployment history for up to 800 deployments. The history is automatically deleted when the count exceeds 775 deployments. 
- Deployment history is a rich source of information. 
- any user with permissions Microsoft.Resources/deployments/read and Microsoft.Resources/subscriptions/resourceGroups/read can read deployment history. 
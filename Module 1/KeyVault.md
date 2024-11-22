- Azure Service for storing secrets like passwords, connection strings, certificates, private keys
- With the right permissions and access, Azure resources that support managed identities can securely retrieve secrets from the key vault. 
Object types avaliable with a key vault 
- cryptographic keys - RSA, EC
- Secrets - passwords, connection strings 
- Certificates - life cycle management 
- Storage account - key vault can manage and rotate access keys for storage account.

## Identifier 
- Objects in a key vault are identified using Object Identifier URL
- The base URL is of format: ` https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`
	- vault-name - is the globally unique name of the key vault
	- object-type - can be 'keys', 'secrets', or 'certificates'
	- object-name - is unique name of object within key vault
	- object-version - is system generated and optionally used to address a unique version of an object.

## Access Management
- access to a vault is controlled though 2 planes:
	- Management Plane - to manage the key vault and access policies only Azure role base access control(RBAC) is supported 
	- Data Plane - to manage the data (keys, secrets and certificates) inn the key vault. This supports key vault access policies or Azure RBAC.
- Please note that a role (like Owner) that has permissions in the management plane to manage access policies can get access to the secrets by modifying the access policies.  
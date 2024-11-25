- blob is used to store unstructured data(files, videos, audio, etc)
- There are 3 types of resources in blob
	- Storage Account -  unique namespace across azure. can be accessed through HTTP or HTTPS
	- Container in the storage account - 'Folders' in the storage account
	- Blob in a Container - Stores data. Tree types of blobs:
		- Block
		- Append
		- Page

## Storage Account
- has globally unique endpoints
- you can guess the storage account name during enumeration:
![[Pasted image 20241121033420.png]]

`https://www.microsoft.com/en-us/security/blog/2021/04/08/threat-matrix-for-storage/`

## Authorization 
- There are multiple ways to control access to a storage account:
	- Entra ID Credentials - Authorize user, group or other identities based on Entra ID authentication. RBAC are supported 
	- Share Key- Use access keys of storage account. this provides full access to the storage account 
	- Shared Access Signature (SAS) - time limited and specific permissions. 

## Anonymous Access 
- by default, anonymous access is not allowed for storage accounts
- if "Allow Blob public access" is allowed on the storage account, it's possible to configure anonymous/public read access to:
	- only the blob inside containers. Listing of container content isn't allowed 
	- Contents of containers and blobs. 

## Storage Explorer 
- is a standalone desktop app to work with Azure storage accounts
- its possible to connect using Keys, SAS urls, etc

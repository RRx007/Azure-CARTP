- if we compromise an Azure resource whose managed identity can read secrets from a key vault, it may be possible to gain access to more resources. 
- Note that each secret has its own IAM inherited from KeyVault 
- overly permissive access policies may result inn access to data stored in a vault.

![[Pasted image 20241121071651.png]]


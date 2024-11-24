- no password hash synchronization of any form to the cloud takes place in PTA but identity synchronization still takes place.
- Useful in enforcing on-prem passwords policies as the authentication is validated on-prem. The communcation with cloud is done by an authentication agent and not the on-prem DC.
- Only outbound communications (Port80 and Port443) from the authentication agent to Entra ID.

![[Pasted image 20241123071853.png]]

# Abuse
- the authentication agent communicates to Entra ID on behalf of on-prem DC. If we compromise the authentication agent, it is possible to verify authentications for any synced user even if the password if wrong
- that is, you ust need valid userPrincipalName and use any password with that! Skeleton key attack for Entra ID. 
- On the other hand if we compromise a Global Administrator, we can install an authentication agent in our own infrastructure that will authorize all login attempts.
` https://o365blog.com/post/on-prem_admin/#pass-through-authentication`


# On-Prem to Cloud
- Once we have admin access to an Entra ID Connect server running PTA agent, run the following  command from AADInternals to insert a backdoor. (Needs to be run as Admninistrator and needs VC++)
` Install-AADIntPTASpy`

- Once the backdoor is installed, we can authenticate as any user synnced from on-prem without knowing the correct password.
- Also, it is possible to see the correct password of on-prem users authenticating on the cloud using the below command on the machine wher the backdoor is installed
` Get-AADINTPTASpyLog -DecodePasswords`
- The DLL used fro injection and passwords are stored, by default, in a hidden directory `C:\PTASpy`
- We can register a new PTA agent after getting GA privileges by setting it on an atttacker controlled machine. Once the agent is setup, we can repeat the previous setps to authenticate using any password and also, get the passwords in clear-text
` Install-AADIntPTASpy`
- Once the backdoor is installed, we can authenticate as any user synced from on-prem without knowing the correct password.
- Also it is possible to see the correct password of on-prem users authenticating on the cloud using the below command on the machine where the backdoor is installed
` Get-AADINTPTASpyLog -DecodePassswords`



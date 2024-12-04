- supports Azure and Entra ID to map attack paths
- `https://github.com/BloodHoundAD/AzureHound`
- it uses the Azure AD and Az PowerShell modules for gathering data through its collectors 

There are 2 free versions of BloodHound 
- BloodHound Legacy 
- BloodHound CE (Community Edition)

### Installation 
- ` https://support.bloodhoundenterprise.io/hc/en-us/articles/17468450058267-Install-BloodHound-Community-Edition-with-Docker-Compose `
- as a prerequisites we need to have Docker and Docker Compose installed:``
```
#update the system
sudo apt update && sudo apt upgrade

#Install Docker
sudo apt install docker.io

# Enable Docker
sudo systemctl enable docker --now

# Install Docker compose
sudo apt install docker-compose
```

- Next we can download and install Bloodhound
```
curl -L https://ghst.ly/getbhce -o bloodhound.yml 

Invoke-WebRequest -Uri https://ghst.ly/getbhce -OutFile .\docker-compose.yaml


# We can use grep to find the password
docker logs $(docker ps --filter "name=bloodhound" -q) 2>&1 | grep "Initial Password Set To:"
```

Make sure that neo4j is running when starting it: (If you do the installation above you won't need the neo4j but in my windows machine i had to setup differently)
```
net start neo4j
```

### Run the collector to gather data:
```
$passwd = ConvertTo-SecureString "SuperVeryEasyToGuessPassword@1234" -AsPlainText -Force
$creds = new-Object System.Management.Automation.PSCredential("test@defcorp.onmicrosoft.com", $passwd)

Connect-AzAccount -Credentials $creds
Connect-AzureAD -Credentials $creds

. C:\AzAD\Tools\AzureHound\AzureHound.ps1
Invoke-AzureHound -Verbose
```

The gathered data can be uploaded to the bloodhound application 

Open the BloodHound application (C:\AzAD\Tools\BloodHound-win32-64x\BloodHound.exe) and login with these creds: 
```
server: bolt://localhost:7687
username neo4j
password BloodHound
```

Upload the ZIP archive to BloodHound UI and use bult-in or custom Cypher queries to query the data.
- `https://hausec.com/2020/11/23/azurehound-cypher-cheatsheet/`
- `https://github.com/LuemmelSec/Custom-BloodHound-Queries`

Cypher Query Examples:
```
Find All Users who have global administrator
MATCH p =(n)-[r:AZGlobalAdmin*1..]->(m) RETURN p

Find all paths to an Azure VM
MATCH p =(n)-[r]->(g:AZVM) RETURN p

Find Owners of Entra ID Groups
MATCH p =(n)-[r:AZOwns]->(g:AZGroup) RETURN p
```

**`https://hausec.com/2020/11/23/azurehound-cypher-cheatsheet/`**


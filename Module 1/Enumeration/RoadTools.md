RoadRecon is a tool  for enumerating Entra ID environments
- ` https://github.com/dirkjanm/ROADtools `

RoadRecon uses a diffrent version '1.61-internal' of AADGraph Api that provides more information.  Allows us to enumerate Conditional access policies

Enumeration using RoadRecon includes 3 steps
- Authentication
- Data Gathering 
- Data exploration

RoadRecon supports username/password, access and refresh tokens, device code flow (sign-in from another device) and PRT cookies.
```
cd C:\AzAD\Tools\ROADTools
.venv\Script\activate

roadrecon auth  -u test@defcorphq.onmicrosoft.com -p SuperVeryEasytoGuesspassword@1234
```

Once Authentication is done, use the below command to gather data (ignore the errors)
` roadrecon gather `

Use roadrecon GUI to analyse the gathered information(starts a web server on port 5000)
` roadrecon gui `

# StormSpotter 
- is a tool from microsoft fro creating attack graphs of azure resources.
- it uses neo4j graph database to create graphs for relationships in Azure and Entra ID

It has the following modules: 
- Backend: this is used for ingesting the data in the neo4j DB 
- FrontEnd: This is the UI used for visualizing data 
- Collector: this is used to collect the data from Azure. 

Start the backend service:
```
cd C:\AzAD\Tools\StormSpotter\backend\
pipenv shell
python ssbackend.pyz


```

In the new Process, Start the frontend webserver:
```
cd C:\AzAD\Tools\StormSpotter\frontend\dist\spa
quasar.cmd serve -p 9091 --history 
```

Use StormCollector to collect the data:
```
cd C:\AzAD\Tools\StormSpotter\stormcollector
pipenv shell
az login -u test@defcorphq.onmicrosoft.com -p <pas>
python C:\AzAD\Tools\StormSpotter\stormcollector\sscollector.pyz cli
```

Log-on the webserver at `http://localhost:9091` using the following:
```
Username: neo4j
Password: BloodHound
Server: bolt://localhost:7687
```

After login, upload the ZIP archive  created by collector and use built-in queries to visualize the data.
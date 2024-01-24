 [![Gitter](https://img.shields.io/badge/Available%20on-Intersystems%20Open%20Exchange-00b2a9.svg)](https://openexchange.intersystems.com/package/iris-fhir-template)

# iris-fhirserver-template
This is an application to participate in the InterSystems FHIR and Digital Health Interoperability Contest. It will create two containers: one with the InterSystems Health Community Edition Database as a FHIR Server and set up with FHIR SQL Builder, which will create some tables allowing easy access to data from the FHIR server. 
It will have another container with a .NET application that will use OpenAI to generate queries for the user. The user will only need to specify the data they want in the prompt, and the application will generate all the queries.

## Prerequisites
Make sure you have [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and [Docker desktop](https://www.docker.com/products/docker-desktop) installed.

## Installation 

### IPM

Open IRIS for Health installation with IPM client installed. Call in any namespace:

```
USER>zpm "install fhir-server"
```

This will install FHIR server in FHIRSERVER namespace.

Or call the following for installing programmatically:
```
set sc=$zpm("install fhir-server")
```


### Docker (e.g. for dev purposes)

Clone/git pull the repo into any local directory

```
$ git clone https://github.com/flnaves/fhir-contest.git
```

Open the terminal in this directory and run:

```
$ docker-compose up -d
```

## Patient data
The template goes with 19 preloaded patents in [/data/fhir](https://github.com/intersystems-community/iris-fhir-server-template/tree/master/data/fhir) folder which are being loaded during [docker build](https://github.com/intersystems-community/iris-fhir-server-template/blob/8bd2932b34468f14530a53d3ab5125f9077696bb/iris.script#L26)
You can generate more patients doing the following. Open shel terminal in repository folder and call:
```
docker run --rm -v $PWD/output:/output --name synthea-docker intersystemsdc/irisdemo-base-synthea:version-1.3.4 -p 10
```
this will create 10 more patients in data/fhir folder.
Then open IRIS terminal in FHIRSERVER namespace with the following command:
```
docker-compose exec iris iris session iris -U FHIRServer
```
and call the loader method:
```
FHIRSERVER>do ##class(HS.FHIRServer.Tools.DataLoader).SubmitResourceFiles("/home/irisowner/irisdev/app/output/fhir", "FHIRSERVER","/fhir/r4")
```

 with using the [following project](https://github.com/intersystems-community/irisdemo-base-synthea)

## Testing FHIR R4 API

Open URL http://localhost:32783/fhir/r4/metadata
you should see the output of fhir resources on this server

## Testing Postman calls
Get fhir resources metadata
GET call for http://localhost:32783/fhir/r4/metadata
<img width="881" alt="Screenshot 2020-08-07 at 17 42 04" src="https://user-images.githubusercontent.com/2781759/89657453-c7cdac00-d8d5-11ea-8fed-71fa8447cc45.png">


Open Postman and make a GET call for the preloaded Patient:
http://localhost:32783/fhir/r4/Patient/1
<img width="884" alt="Screenshot 2020-08-07 at 17 42 26" src="https://user-images.githubusercontent.com/2781759/89657252-71606d80-d8d5-11ea-957f-041dbceffdc8.png">


## Testing FHIR API calls in simple frontend APP

the very basic frontend app with search and get calls to Patient and Observation FHIR resources could be found here:
http://localhost:32783/fhirUI/FHIRAppDemo.html
or from VSCode ObjectScript menu:
<img width="616" alt="Screenshot 2020-08-07 at 17 34 49" src="https://user-images.githubusercontent.com/2781759/89657546-ea5fc500-d8d5-11ea-97ed-6fbbf84da655.png">

While open the page you will see search result for female anemic patients and graphs a selected patient's hemoglobin values:
<img width="484" alt="Screenshot 2020-08-06 at 18 51 22" src="https://user-images.githubusercontent.com/2781759/89657718-2b57d980-d8d6-11ea-800f-d09dfb48f8bc.png">


## Development Resources
[InterSystems IRIS FHIR Documentation](https://docs.intersystems.com/irisforhealth20203/csp/docbook/Doc.View.cls?KEY=HXFHIR)
[FHIR API](http://hl7.org/fhir/resourcelist.html)
[Developer Community FHIR section](https://community.intersystems.com/tags/fhir)

## What's inside the repository

### Dockerfile

The simplest dockerfile which starts IRIS and imports Installer.cls and then runs the Installer.setup method, which creates IRISAPP Namespace and imports ObjectScript code from /src folder into it.
Use the related docker-compose.yml to easily setup additional parametes like port number and where you map keys and host folders.
Use .env/ file to adjust the dockerfile being used in docker-compose.


### .vscode/settings.json

Settings file to let you immedietly code in VSCode with [VSCode ObjectScript plugin](https://marketplace.visualstudio.com/items?itemName=daimor.vscode-objectscript))

### .vscode/launch.json
Config file if you want to debug with VSCode ObjectScript


## Troubleshooting
**ERROR #5001: Error -28 Creating Directory /usr/irissys/mgr/FHIRSERVER/**
If you see this error it probably means that you ran out of space in docker.
you can clean up it with the following command:
```
docker system prune -f
```
And then start rebuilding image without using cache:
```
docker-compose build --no-cache
```
and start the container with:
```
docker-compose up -d
```

This and other helpful commands you can find in [dev.md](https://github.com/intersystems-community/iris-fhir-template/blob/cd7e0111ff94dcac82377a2aa7df0ce5e0571b5a/dev.md)

 [![Gitter](https://img.shields.io/badge/Available%20on-Intersystems%20Open%20Exchange-00b2a9.svg)](https://openexchange.intersystems.com/package/iris-fhir-template)

# FHIR Data Query Application with OpenAI Integration
## Overview
This repository hosts a cutting-edge application designed to revolutionize how users interact with healthcare data. The application comprises two primary components, each running in its own container: a database utilizing the InterSystems FHIR technology, configured with tables via the FHIR SQL Builder, and a .NET application with a front-end interface.

## Components
### Container 1: InterSystems FHIR Database
Functionality: This container runs an InterSystems FHIR database. The database schema is meticulously crafted using the FHIR SQL Builder, ensuring an optimized structure for storing and retrieving patient information efficiently and securely.

### Container 2: .NET Application
Front-End Interface: The .NET application features a user-friendly front-end interface. Users can input their data queries into a text box, making the process of data retrieval straightforward and accessible.
Integration with OpenAI: Utilizing the OpenAI API, the application translates natural language queries into precise SQL commands. This innovative approach allows users without SQL knowledge to interact with the database effortlessly.
Data Retrieval: The generated SQL queries interact with the InterSystems FHIR database to fetch requested patient data, demonstrating a seamless integration between the front-end interface and the database.


## Prerequisites
Make sure you have [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and [Docker desktop](https://www.docker.com/products/docker-desktop) installed.

## Installation 

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

## What's inside the repository

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

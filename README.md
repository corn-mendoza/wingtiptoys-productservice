# Wingtip Toys Product Service

Demo .NET Core 3.1 WebAPI Service leveraging Steeltoe and accessing a SQL Server database. This application can use the same database that is created as part of the original WingTip Toys .NET Framework application. Scripts are also included in the _scripts_ folder to create and populate a standalone version of the database.

## Overview

Standalone project that can be used with Azure DevOps, Tanzu Build Service, Docker, Cloud Foundry, or Tanzu Kubernetes Grid. 

## Requirements

This project is part of the WingTip Toys Store application designed to demonstrate Cloud Native principles using .NET Core. The product service requires the connection string to access the WingTip Toys DB hosted on SQL Server.

## Accessing the Product Service

The Swagger UI has been implemented as part of the project and can be accessed at http://route-to-app/swagger/ after the service has been deployed.


# Building the Service

## Tanzu Build Service

The following is an example of steps to build using TBS. To build the application image and deploy to a repository, create an image configuration file for the application.

Sample **wingtipcore-products-config.yml**

    source:
      git:
        url: https://github.com/corn-pivotal/wingtiptoys-productservice
        revision: master
    image:
      tag: cjmendoza/wtt-product-service

Sample **dockerhub-config.yml** secrets file

    project: wingtipcore
    registry: https://index.docker.io/v1/
    username: xxxxxxxxx
    password: xxxxxxxxx

Sample **github-config.yml** secrets file

    project: wingtipcore
    repository: github.com/corn-pivotal/wingtipcore
    username: xxxxxxxxxxx
    password: xxxxxxxxxxx


To build using TBS:
1. Ensure TBS is available by running: 

    `pb stack status`
    
2. Create a project in TBS called _wingtipcore_

    `pb project create wingtipcore`
    
3. Set the target project to wingtipcore

    `pb project target wingtipcore`
    
4. Add users to project

    `pb project user add <username>`
    
5. Load secret files for accessing git and the target repository

    `pb secrets registry apply -f secrets/dockerhub-config.yml`
    
    `pb secrets git apply -f secrets/github-config.yml`
    
6. Run the build

    `pb image apply -f wingtipcore-products-config.yml`


## Cloud Foundry

A sample manifest file has been included to allow the app to be deployed using the cloud foundry cli and "cf push" command.

To push the application, run the dotnet publish command in the project directory:

    dotnet publish -o <output directory>
    
 Navigate to the publish directory specified in the dotnet publish command and run:
 
    cf push

or

    cf push <appname>
    

## Azure Devops

A base azure-pipelines.yml file has been included in this project. The pipeline will need to be updated to also deploy the built container into an accessible repository.

For a great guide to install Azure DevOps build agent on your local kubernetes cluster, check out [Luciano's Guide](https://github.com/lsilvapvt/pcf-tools-belt/tree/master/azure/devops/agent).


## Docker

A base Dockerfile has been included in this project to allow the application image to be built using Visual Studio or the Docker cli.

To build from the command line in the project directory:
    
    docker build -f dockerfile --force-rm -t wingtiptoysproductservice <path to source>


# Deploying the Product Service

This application is designed to load secrets from an optional file located in **secrets/appsettings.secrets.json**. All platforms can leverage this to inject sensitive SQL Server connection string information for the application or continue to use the appsettings.json file.

Sample **appsettings.secrets.json**

    {
        "ConnectionStrings": {
            "WingtipToysProductServiceContext": "<yourconnectionstring>"
        }    
    }  

    
## Kubernetes

1. Add the secret:

    `kubectl create secret generic secret-appsettings --from-file=./appsettings.secrets.json`
    
2. Create a deployment file for the application **deployment.yml**


        apiVersion: apps/v1
        kind: Deployment
        metadata:
            name: wingtiptoys-product-service-demo
        spec:
            replicas: 3
            template:
                metadata:
                    labels:
                        app: wingtiptoys-product-service
                spec:
                    containers:
                    - name: wtt-product-service
                      image: cjmendoza/wtt-product-service:latest
                      ports:
                    - containerPort: 80
                    env:
                    - name: "ASPNETCORE_ENVIRONMENT"
                      value: "Production"
                    volumeMounts:
                    - name: secrets
                      mountPath: /app/secrets
                      readOnly: true
                volumes:
                - name: secrets
                  secret:
                    secretName: secret-appsettings


3. Deploy the service using the deployment file.

    `kubectl create -f deployment.yaml`
    

## Cloud Foundry

This service will take advantage of SCS Discovery Services if the application is bound via the service broker.

# wingtiptoys-productservice
Demo .NET Core WebAPI Service

## Overview
Standalone project that can be used with Azure DevOps, Tanzu Build Service, Docker, Cloud Foundry, or Tanzu Kubernetes Grid. 

## Requirements
This project is part of the WingTip Toys Store application designed to demonstrate Cloud Native principles using .NET Core. The product service requires the connection string to access the WingTip Toys DB hosted on SQL Server.

## Building the Service

### Azure Devops
A base azure-pipelines.yml file has been included in this project. The pipeline will need to be updated to also deploy the built container into an accessible repository.

For a great guide to install Azure DevOps build agent on your local kubernetes cluster, check out [Luciano's Guide](https://github.com/lsilvapvt/pcf-tools-belt/tree/master/azure/devops/agent).

### Tanzu Build Service
To build the application image and deploy to a repository, create an image configuration file for the application.

Sample wingtipcore-products-config.yml

    source:
      git:
        url: https://github.com/corn-pivotal/wingtiptoys-productservice
        revision: master
    image:
      tag: cjmendoza/wtt-product-service

### Docker
A base Dockerfile has been included in this project to allow the application image to be built using Visual Studio or the Docker cli.

### Cloud Foundry
A sample manifest file has been included to allow the app to be deployed using the cloud foundry cli and "cf push" command.


## Deploying the Service

### Kubernetes

### Cloud Foundry

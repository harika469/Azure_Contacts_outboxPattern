# Sample implementation of the Transactional Outbox Pattern with Cosmos DB

This sample application consists of a "Contacts" API and an event processor that reads a Cosmos DB Change Feed and publishes domain events to an Azure Service Bus. The sample demonstrates how to use the **Transactional Outbox Pattern** in combination with **Azure Cosmos DB** to implement **reliable/guaranteed messaging** in your microservice-oriented application.

The source code is structured as follows:

- **Contacts.Domain** - Contact domain logic and interfaces
- **Contacts.Infrastructure** - Implementation of domain interfaces like repositories
- **Contacts.Application** - Implementation of CQRS pattern using MediatR library, validation and mapping logic
- **Contacts.API** - RESTful API consuming application logic components
- **Contacts.EventsProcessor** - Console application using the Change Feed Processor library to read the Cosmos DB change feed and publish domain events to Azure Service Bus

## Prerequisites

To run the sample application on your local machine, you need to have the following components installed:

- [.NET 8.0 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- [Visual Studio Code](https://code.visualstudio.com/download)

You also need to create some Azure resources:

- Azure Cosmos DB
- Azure Service Bus
- Azure Function (to test event distribution via Service Bus - acts as a subscriber)

You can find a bicep file that will take care of creating these resources (and their dependencies) under [Deployment/deploy.bicep](Deployment/deploy.bicep).

To run the deployment script, open an [Azure Cloud Shell](https://shell.azure.com), 
clone this repository to your environment - git clone <repository_url> 
 switch **to the `Deployment` folder**  - cd repositoryName/deployment 
 and run below script : 

```shell
export RESOURCE_GROUP=<resource-group-name> # name for your resource group
export LOCATION=<azure-region> # Azure region, for example westeurope, westus etc.

az group create -n $RESOURCE_GROUP -l $LOCATION

az deployment group create -f deploy.bicep -g $RESOURCE_GROUP -o none

az deployment group show -g $RESOURCE_GROUP -n deploy --query properties.outputs
```

The last command will output the relevant parameters that you need to replace in the following files:

- [Contacts.API/appsettings.json](Contacts.API/appsettings.json) - add the Cosmos Url and access key details 
- [Contacts.EventsProcessor/appsettings.json](Contacts.EventsProcessor/appsettings.json) - add service bus connection string and Cosmos url 


## Clean-Up

To clean-up the Azure resources, use the following command:

```shell
az group delete -n $RESOURCE_GROUP
```

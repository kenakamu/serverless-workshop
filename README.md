# Azure Serverless Workshop

## Overview

Imagine spending your time building and deploying great applications without the burden of managing servers. This is possible with serverless computing, where you no longer have to plan for or manage the infrastructure required to support your apps. With serverless, you can focus your efforts on the business logic and innovation that will bring your great apps to market faster.

In this full-day, challenge-based workshop, you will learn about designing and building serverless solutions on Azure. You’ll experience hands-on time writing and publishing your code with [Azure Functions](https://azure.microsoft.com/en-us/services/functions/), building workflows and integrating with other services using [Logic Apps](https://azure.microsoft.com/en-us/services/logic-apps/), and protecting and managing your APIs with [Azure API Management](https://azure.microsoft.com/en-us/services/api-management/), and building reactive, event-driven solutions using [Event Grid](https://azure.microsoft.com/en-us/services/event-grid/). We will even share a few tips for a solution to migrate serverless workloads with just a few lines of code using the [Serverless Framework](https://serverless.com/)!

Your code, logic, and imagination is all you need to bring – the serverless platform will take care of the rest.  

## Target Audience

* This event is targeted at software engineer / developer roles.  However anyone interested in learning more about Azure and serverless functionality is welcome to attend.  Please review the [prerequisites](./prereqs.md) to ensure your environment is ready for the workshop.

* This event is especially beneficial to those that work with organizations who are planning to build serverless architectures on the cloud or organizations planning to automate business processes.

## Preface

<img style="float: right;" height="160" src="https://serverlessoh.azureedge.net/public/ice-cream-2202561_320-circle.jpg" />

**Best For You Organics Company (BFYOC)** is an ice cream company with over 200 stores worldwide. BFYOC has focused on fresh ingredients, locally sourced, and seasonal. Their 26 flavors range from the ordinary vanilla, to the ever popular avocado toast (which is a much better flavor than it sounds). They are now creating a new line of fruit flavored ice creams and are preparing to launch onto the market.

Because premium ingredients are key to their success, their profit margins are relatively low, as they try to keep prices affordable for consumers. As such, every sale needs to be optimized, and every customer needs to leave happy. They also need to cut costs whenever possible, which of course impacts their budget for new technology.

BFYOC would like to launch a new customer feedback tool to analyze their sales and customer satisfaction levels. Because feedback posts from customers will be sporadic, they are looking for options that can automatically scale, and minimize investment in infrastructure.

## Challenges

Over the next [several hours](./agenda.md), you will use multiple Azure Serverless components to build an API which will be a key part of BFYOC's new customer feedback tool.

| Challenge                      | Description       |
|--------------------------------|-------------------|
| Azure Function Basics          | Create a basic HTTP-triggered Azure Function and deploy it to Azure.  |
| Cosmos DB and Azure Functions  | Create a HTTP-triggered Azure Function which uses output bindings to persist data to Cosmos DB.  |
| Logic Apps                     | Create a Logic App workflow which accepts user feedback via an HTTP post, detects the sentiment of the feedback, persists the feedback in a Cosmos DB database, and sends an email for unfavorable feedback.  |
| API Management                 | Use API Management to provide a uniform API endpoint, as well as API versioning and rate limiting.  |
| Event Grid                     | Use Event Grid to publish events to multiple subscribers whenever customer feedback is received.  |
| Serverless Framework           | Use the Azure plug-in for Serverless Framework to explore an alternative approach to creating and deploying an HTTP-triggered Azure Function which uses Cosmos DB.  |

### Challenge Path

The diagram below provides an overview of your journey. You can choose to complete the challenges sequentially, from challenge 1 through to challenge 5.  Alternatively, you can elect to complete the Serverless Framework challenge after challenge 2 (since the two challenges are very similar), and then continue with challenge 3.  You can think of challenge 2b as an exciting detour before continuing on your Azure Serverless journey.

![Challenge Path Overview](./Images/challenge-path.png)

## Next

Are you ready?  Proceed to your first challenge - [creating and deploying an Azure Function](./Challenge-1-Azure-Function-Basics/readme.md).

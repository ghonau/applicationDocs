#Azure Container App
1- Deploying API Endpoint
2- Hosting background processing applications
3- Handling event-driven processing
4- Running Microservices

It can scale dynamically based on:
1- Http traffic
2- event-driven processing
3- CPU or Memory Load
4- KEDA-supported scaler

you can
1- Run multiple container revisions, manage container app's application lifecycle
2- Autoscale
3- Enable Https ingress without having to manage other Azure infruscture
4- Split traffic across multiple versions of an application for Blue / Green deployments and A/B testing
5- Use internal ingress and service discovery for secure internal only endpoints
6- Build microservices with dapr
7- Run containers from any registry
8- Use Azure CLI extension, Azure portal, ARM templates to manage your application
9- Provide your existing network when creating an environment for your container apps.
10- Securly manage secrets directly in your application
11- Monitor logs using Azure log analytics

##Azure container app environment
it is secure boundary around groups of container apps.

Container apps in the same environment are deployed in the same virtual network and write logs to the same Log Analytics workspace.

benefits:
1- Manage related services
2- Deploy different applications to the same virtual network
3- Instrument Dapr applications that communicate via Dapr service invocation API
4- Have applications to share same dapr configuration
5- Have application to share same Log Analytics workspace

Reason to deploy container apps to different environments
1- Two application never share the same compute resources
2- Two Dapr applications cannt communicate via Dapr service invocation API

##Dapr integration
In the microservices solutions you need to account for failure, retries, timeouts, While the container apps features the builing blocks for running microservices, use of Dapr provides an even richer microservices programming model.

Dapr includes features like observability, put/sub, service to service invocation with mutual TLS and retries.

**We register providers with their namespace**
az provider register --namespace Microsoft.OperationalInsights

###Deploy container app
1- We will create container app environment
2- You Container App Environment deployment you can deploy an application image by using container app create command

### Explore containers in Azure Container Apps

Azure container apps manages the details of Kubernetes and container orchestration for you.

### Multiple containers

You can define multiple containers in a single container app to implement the sidecar patterns the containers in a container app share hard disk and network resources and experience the same application lifecycle.

Exmple of sidecar containers

1-An agent that reads logs from the primary app container on a shared volume and forwards them to a logging service
2- A background process that refreshes a cache used by the primary app container in a shared volume.

### Container registry Limitations

1-Privileged containers: Azure container apps cannot run privileged containers. if your application attempts to run a process that requires root access, the application inside the container experience a runtime error

2- Operating System: Linux-based container images are required.

### Implement authentication and authorization in Azure Container Apps

1- Provides built-in authentication and authorization features to secure your external ingress-enabled container app with minimal or no code. the out of the box federated identity providers allowing you to focus on the reset of your application.

it provides access to various built-in authentication providers
The built-in auth feature do not require any particular language, SDK, Security Expertise, or even code.

This feature should only be used with Https. Ensure allowInsecure is disabled on your container app's ingress configuration.

#### How to configure

To restrice app access to only authenticated users, set its _Restrict access_ to **Required authentication**
To authenticate but not restric access, set its _Restrict access_ set to **Allow unauthenticated** access

#### Identity providers

Container apps uses federated identity, in which a third-party identity provider manages the user identities and authentication flow for you.

Authentication and authorization middleware component is a feature of the platform that runs as a sidecar container on each replica in your application.

### Authentication flow

the authentication flow is the same for all providers, but differs depending on whether you want to sign in with the provider's SDK

### Manage revisions and secrets in Azure Container App

A revision is an immutable snapshot of a container app version.
you can use revisions to release a new version of your app or quickly revert to an earlier version of your app.

Revisions are created when you update your application with revision-scope chagnes.
You can control which revisions are active and the external traffic that is routed to each active revision.

#### Updating your container app

With the az containerapp update command you can modify environment variables, compute resources, scale parameters and deploy a different image. if your container app update includes revision-scope changes a new revision is generated.

#### Manage secrets in Azure container app

it allows to securly store sensitive configuration values. Once the secret are defined at the application level, secured values are available to container apps.

1- Secrets are scoped to an application, outside of any specific revision of an application
2- Adding, removing or changing secrets does not generate revisions
3- Each application revision can reference one or more secrets.
4- Multiple revisions can reference the same secrets.

**Before you delete a secret, deploy a new revision that no longer reference the old secret, Then deactive all revisions that reference the secret.**

**Container Apps does not support Azure key vault integration. instead, enabled managed identity in the container app and use the Key Vault SDK in your app to access secrets.**

secrets in the --secret are space-delimited set of name/value pairs and Each pair is delimited by an = sign.

To reference a secret in an environment variable in the Azure CLI, set its value to secretref: followed by the name of the secret

az container app create
--secret "queue-connection-string=$ConnectionString
--env-vars "QueueName=myqueue" "ConnectionString=secretref:queue-connection-string"

## Explor Dapr integration with Azure container APPs

Dapr provide intercommunication capabilities through messaging via pub/sub or reliable and secure service-to-service calls.

####Container apps platform provides:
1- Provides managed and supported Dapr integration
2- Handles Dapr version upgrades seamlessly
3- Exposes a simplified Dapr integration model to increase developer productivity

The fully managed Dapr APIs are exposed to each container app through a Dapr sidecar. The Dapr APIs can be invoked from your container app via HTTP or gRPC. The Dapr sidecar runs on HTTP port 3500 and gRPC port 50001.

Dapr components can be shared across multipe container apps.

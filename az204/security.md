# az204

## security

### active directory

#### windows ad is a identity provider after authentication against identity provider you can authorize to use app

#### azure ad is a cloud-based identity and access management service

##### can be used a identity provider for Auzre, D365 and other Saas products

##### you can manage identities such as users, groups, or even applications

#### pricing tier

- Free user and group management
- Prmimum p1 dynamic groups, hybrid identities, self-service password reset for on premis users
- preimum p2 Azure Ad identity protection and priviledge Identity management

## Create a new user

### new user will need to change the password upon first login

### we can use resourceID (url) as a scope and identity and role to create role assignment object in Ad

## application object

1- you can use access key to access the storage account in your application

2- or you can create application object in AD and give rbac to the application object to access to the storage account

3- application uses **Microsoft Identity platform** to do the authentication

4- application object can be given the rolebased access control just like a user and it can access to the resources in azure without having to have access key in the connection string

5- to use application role based access control we can use the following Identifiers to access to the resources

- client Id or applicationId
- tenant Id
- **client secret** with this client secret that our .net application will have we identity our .net application to be known application with application object in AD and we can use the same access level as the application object in AD for our .net application
- and then we can use **ClientSecretCredentital** which is part of the azure identity

## Microsoft Graph

1- In order to use _Microsoft Graph API_ we need to authorize first
2- In order to authorize the client to call _API_ you will need to use **OAuth** with **Access Token**
3- If you want to give access to AAD you cannot use normal Access Control like other resources
4- Once you have created an app there is a section called API permission on the app registration with which we can assign permission to run some APIs like graph API with scope for the app object created in Azure AD
4- There are two types of permissions _Application_ and _Delegated_ persmissions
5- On Azure AD application object we provided API permission for the app as opposed to rbac that we could use for other azure resources _App could be give the contributor role on storage account for example_

6- We will need to get **Access Token** for postman application to be able to call the API that we have given permissions to
7- Once you have received access token from the end point based on the API permissions you have given to your application you can use the **Access token** to run the API that your application object has permission
8- Generated _Access Token_ has API permissions incorporated in it.

### Key vault

#### Keys

We can use the key in the keyvault to encrypt and decrypt texts with the encrypt and decrypt permission for the app registered in ad and we do have the client secret for the app to create client credentail

#### Secrets

Instead of embeding the secret in the application we can simply put the secret like connection string or password in the secret section of the keyvault

### Managed Identity

This will help azure services to authenticate to other azure services that support azure AD authentication

We could use managed identity to avoid storing access key to access the resources

We create idnetity which will reside inside the AD we will not store anything about the identity and we will give permission to the identity to access to other resources and we will assign the identity to our app registed in AD
so the registed app will be assigned to identity and the identity will have access to the other resources

this is only useful for the application in the Azure
if you have the app in your local machine app object with the permission to access to the other resources and local app with the knowledge of client secret would be a good choice

## Create Managed Identity

1- Select your resource (VM)
2- Go to Identity Section
3- Select System Assigned and status On and hit Save. it will register the VM with Azure AD so then you can grant permission to the VM to access to other resources. Once you register a managed Identity with the resource that resouce becomes a app object in AD and you can give permission to the app which is like a giving access to managed identity

4- We can create managed Identity on resources like VM or App services and then give enough permissions to those managed Identity to Get data from key vault with access policy just an example and then run the app deployed to azure so it will be able to use **DefaultAzureCredential** in the azure deployment to use identity associated with VM or APP service and access to the keyvault

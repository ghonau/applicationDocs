# Oauth2.0

it is a industry-standard protocol for authorization
we will be giving access to the user who logged-in to the application
and wants to access to some of the resources, in this scenario we are **Not** giving access to the application via app object or managed identity

_we are giving access to the user and app will use the permissions granted to the user to access to azure resources like storage account_

this is called authorization code flow

use cases

1- Resource Owner - User is a resource owner and has access to the resources
2- Client - This is a application requesting access to the protected resource
3- Resource server - In Azure, this can be a Web API that will allow access onto the Azure resource like Azure Storage Account

## Authorization Server

Microsoft Identity platform is a authorization server it manages end-users information, their access and also issues security tokens

## Authorization code flow

1- Application send a request with redirect url

2- Authorization server will create authorization code and return the code to the redirect Url the authorization code is actually viewable in the browser

3- Once the application receives the code in the redirected Url which is componenent of the application it will use the code and send another request with the same redirect url in the request to get the access token from the authorization server

**_ exchanging authorization code with access token will happen with web application in the backend with server side execution _**

We using app object registered in Auzre you can go to authenticate section and specify what type of application will make a use of the app object

### ID token

During setting the authentication section of the app registration you can select Id token and it will do the authenticatiion we also can have claims in the token

#### Azure AD groups & application role

you can create a Azure group and assign users to the group and you application has many different roles to do different things in the application

#### What we do

1- We create a Azure Group asign users to the group  
2- We will go to **Token Configuration** section on the app registration and add groups claim for security groups
3- Application will see the group coming in the ID Token
4- Application can translate group to the role and implement role-based authorization based on the values in the group

### Access Token

#### Access to storage account

app in Auzre will need to have delegated permission type to use Storage account API (eg.scope) to be able to privide access to the containers in the storage account for the logged in user

1- Give the user read access to the storage account
2- Give the user blob reader permission
3- .Net application will need to get access token so we will need to add scope "Url to the Storage Account" with which we can give requried API permission to the app with the delegated permission type to read the storage data
4- Application will take the permission of the user and use it to access to the resources that user has access to. application impersonates as a user. Application will have access to the storage account on behalf of the logged in user.
5- Application will need a API permission with delegated type to use logged in user permission to access to the resources
6- we also need to tick Access Token checkbox on the ""Authentication"" section of the app registration

6- .NET Application will need to have client secret to be able to get access token as it needs to be recognised by the azure AD and the app

7- if you are using post man to get the blob we will need to have x-ms-version 2017-11-09 along side of the access token in the request.

8- Create a app registration for your custom api

#### Protecting Custom API

1- You creata a app registration with no redirect urI
2- Go to Manifest of the app change accessTokenAcceptedVersion from null to 2

3- Add Microsoft.Identity.Web package and you do not need to add Microsoft.Identity.Web.UI as there is no UI for the API
4- add builder.Services.AddMicrosoftIdentityWebApiAuthentication(builder.Configuration, "AzureId)
5- Add the following configuration in the appSettings.json file
"AzureAd" : \_{
instanceId: "https://login.microsoftonline.com"
clientId: ""
tenantId: ""
}
6- We will not **Client Secret** here as Web API does not need access token
and the client app will need access token not the API app
7- Then add app.UseAuthentication() and app.UseAuthorization()
8- For the Product API app we will need to create a App Role with that role we will allow apps to interact with the Product API app.
9- Then we can use Application ID URI and expose an API
10- Adding scope will only create delegated permissions that we do not want it and will be using Aplication ID URI
11- Client App will use clientID, Client Secret and Scope which is the Application ID URI of the Product API and it is basically scope that we are applying to get the access token for.

11- For the client app like Postman app you will see in the API Permissions my own API that Product API is there and we give Application Permissions to with the role that we have created on the API role section to give permission for the client app or Postman app.

##### Accessing Storage Account API from Console App

We will need to get to access token first and use it to access to the Storage account API
1- We will need to provide app details like clientId, tenantId, clientSecret for one of the app in the console app code
2- We will use ConfidentialClientApplicationBuilder to pass the details and create **ConfidentialClientApplicationBuilder**
3- Then we will need to use scope which is the same as Application ID URI for the product API
4-ConfidentialClientApplication can use the scope and get the access token for the scope
5- Use HttpClient and pass Bearer Token to the DefaultRequestHeader object on the HttpClient object

##### Accessing Storage Account API from web application

1- For the web application you will need to go to the Authentication section and set the redirect URL to use signin-oidc
2- We will need to go to Expose an API on the Proudct API and create a scope which will be for delegated persmission

3- On the AuthApp we are using for the web application we will need to go the API permissions and add a permission on the my own permissions

4- We can then use newly created scope and then run GetAccessTokenForUserAsync(scope) on the TokenAcquistion method

##### App role claims

It is a custom role to assign permissions to the users and apps. The application defines and publishes the app roles and interprets them as permissions during authorization

1- Create a app role and set **Allowed member types** to users/groups
2- Then you go to the Enterprise applications in the AD
3- Select the app you just create a custom role for it
4- go to **Users and groups** and add new user or group

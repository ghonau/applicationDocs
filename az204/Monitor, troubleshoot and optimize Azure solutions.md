# Azure Monitor

## Alert

we can using exsiting Monitor | Metrics

We can also use the existing Monitor | Alert to create alert
1- Choose scope
2- Select Signal type

    All
    Metrics "CPU goes over 80%"
    Log
    Activity Log like some one "Power off Virtual Machine"

3- We can use static treshold or dynamic treshold to trigger the alert

### Dynamic Treshold

Azure monitor uses machine learning to check the historical behavior of metrics
Based on historical data Azure monitor can detect anomolies that could indicate possible issues

### Log Analytics Workspace

it is a centeral solution for all of your logs
We create Log Analytics Workspace and then connect it to the resources

#### Connect to vm

1- you need to go to Workspace Data Sources and select Virtual Machines
2- Once you selected your VM then hit Connect button
3- It will install Microsoft Monitoring agent in your VM if you go to the **Extensions + applications** section on the VM
4- Then you can go to **Agents configuration** section on the log analytics workspace and select what is the source for the logs could be Windows Event Logs or Windows Performance Counter or Linux Perfomance counter or even IIS Logs

#### Azure Web apps Dignostics

you can send Azure resources Diagnostics data to log analytics workspace as well

##### Web apps to Log Analytics Worksapce

1- Go to Web App and then **Diagnostic Settings**
2- You can choose what type of logs needs to be collected AppServiceHttpLogs, AppServiceAppLogs and then what is the destination for those log you can set to **Send to Log Analytics workspace**

### Application Insights

#### How it work

you install a small instrumentation package (sdk) for your application OR use Application Insights agent.

telemetry data send by Application Insights has very little impact on your application performance

#### Send query in the application insights

1- ConfigureTelemetryModule<DependencyTelemetryModule>((m,o) => m.EnableSqlCommandTextInstrumentation = true)

#### usages

1- User
2- Events
3- Cohert
4- Flow

#### Availability Tests

You can define tests that can monitor the avaialibity and responsiveness of an application

##### Web Requests

This feature can send Web Requests to your web application from different points across the world

##### Endpoints

Setup tests againts an Http or Https endpoint that's accessible over the public internet

##### API

you can also test your REST API as well

### Azure cache for Redis

#### Data store

1- in memory data store based on redis software.
2- you can strore frequently accessed data in memory
3- you can read and write
4- it helps to provide low latency to your data and high throughput

### CDN Profile

Content will be picked up from the point of presence and may be stale data.

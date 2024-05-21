<h3>Azure UpdateManager OpenAI integration: Configuration</h3>
 
| **Parameters** | **Information** | **Note** |
| ------------- | ------------- | ------------- |
| **Parameters** | **Information** | **Note** |
| ------------- | ------------- | ------------- |
| replacewithsubid | Connection setting during deployment | Replace with your Subscription ID |
| repreplacewithRG | Connection setting during deployment | Replace with the selected RG Name for the deployment |
| api-key | The API code for manage your OpenAI service | The parameter is inside the second "Initialize Variable". Put your question in the "value" attribute  |
| changeendpointname | Insert the OpenAI endpoint name | You can found the value inside the OpenAI resource inside Azure Cognitive Service |
| changemodelname | Insert the model name | You can found the value inside the OpenAI resource inside Azure Cognitive Service |

<h3> Important </h3>
 This LogApp and the following changes are an example of integrating UpdateManager results with OpenAI, creating a report send via Email with OpenAI comment of pending Security Update of your environment. 
 
 
<h3>Deploy</h3>

When you deploy, replace with your SubscriptionID and ResourceGroup Name:

![iDeploy](./images/deploy.jpg)


<h3>Required Identity</h3>
<h4>Managed Identity</h4>

When the deployment is completed go in your Logic App and create a Managed Identity following the example below and give them __LogAnalytics Reader__ permission:

![identity](./images/identity.jpg)


<h3> Deployment and Result </h3>
 
After deployment completed, please follow the documentation:


Change __Recurrence__ section after SQL BPA has been performed:

![recurrence](./images/recurrence.jpg)


Configure the Api Key with the value inside your OpenAI Service:

![Sentinel Apy Key](./images/ApiKey.jpg)

Configure __ForeachSQLResult__ section with value of query result and each parameter in Question variable:

![SQL BPA question](./images/query-value.jpg)
![SQL BPA question](./images/value-question.jpg)

Configure the severity level as a condition filter as you prefer:

![SQL BPA question](./images/severity.jpg)

Now configure the HTTP Connector for OpenAI Connection following this configuration:

![Sentinel HTTP Connector](./images/http-connector.jpg)

The last configuration is change the broken module __Send an email (V2)__ with a new one, add the attachment and customize the file name :

Before <br>
![Sentinel Add Content](./images/sendEmail-broken.jpg)

After <br>
![Sentinel Add Content](./images/send-email2.jpg)

Now save your LogicApp and __Enable__
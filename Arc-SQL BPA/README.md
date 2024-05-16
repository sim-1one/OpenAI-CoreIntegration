<h3>Azure Hybrid Group Membership Notification: Configuration</h3>
 
| **Parameters** | **Information** | **Note** |
| ------------- | ------------- | ------------- |
| replacewithsubid | Connection setting during deployment | Replace with your Subscription ID |
| replacewithRGname | Connection setting during deployment | Replace with the selected RG Name for the deployment |
| replace with tenant id | HTTP Module: Tenant ID | Replace with your Tenant ID |
| replace with clientid | HTTP Module: Service Principal Client ID | Replace with Service Principal Client ID |
| replace with secret | HTTP Module: Service Principal Secret ID | Replace with Service Principal Secret ID |
 
N.B. The solution require a Storage Account for store the logs about audit logs.
 
<h3> Deployment and Result </h3>
 
Durint the deployment please change the required value inside the connection string with the subscription id and the resource group name. After that, when the deployment is completed, please follow the documentation:
 
Change che API connection with a new one following your requirement. The solution must have permission to write and read the storage account.
 
<img src="https://i.ibb.co/SdP3K8p/connection1.png" alt="Connection" title="Connection">
 
The first "Get blob content (V2)" block must be configured with the final name of the blob (read file) that will store the Delta URL Link (in yellow). Please Use the same Blob Name for all the Blob Blocs:
 
<img src="https://i.ibb.co/PDG15vz/containerconfig.png" alt="containerconfig" title="containerconfig">
 
The Flow have different HTTP blocks required for get informations from Azure Graph. Please exand all the foreach blocks and customize the HTTP blocks as shown below. Keep in mind that the chosen solution must have permission to read Azure Graph. Ensure to have a Service Principal with all the required permission <a href=https://learn.microsoft.com/en-us/graph/api/user-get?>reported here</a>  and <a href=https://learn.microsoft.com/en-us/azure/purview/create-service-principal-azure>here</a>. For a tutorial please refer to <a href=https://techcommunity.microsoft.com/t5/azure-integration-services-blog/calling-graph-api-from-azure-logic-apps-using-delegated/ba-p/1997666>this link</a>:
 
<img src="https://i.ibb.co/ZTPXx8t/HTTPrequest.png" alt="HTTPrequest" title="HTTPrequest">
 
Remember to change the group name inside the foreach block in order to check only modification to the required group:
 
<img src="https://i.ibb.co/VgsHD4b/groupname.png" alt="groupname" title="groupname">
 
The last step is to add and customise the Send e-mail form (V2) to send an e-mail with the requested information. You can customise it as you wish. The block must be placed at the end of the logic app flow.
 
<img src="https://i.ibb.co/MG4GQJd/email.png" alt="email" title="email">
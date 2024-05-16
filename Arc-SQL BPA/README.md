<h3>Azure SQL BPA OpenAI integration: Configuration</h3>
 
| **Parameters** | **Information** | **Note** |
| ------------- | ------------- | ------------- |
| replacewithsubid | Connection setting during deployment | Replace with your Subscription ID |
| replacewithRG | Connection setting during deployment | Replace with the selected RG Name for the deployment |
| replace with tenant id | HTTP Module: Tenant ID | Replace with your Tenant ID |

 
 
<h3> Deployment and Result </h3>
 
After deployment completed, please follow the documentation:

Change the broken module Run query and list result with a new one.
 
Before: <br>
![Run query and list result](./images/run-query-list-result1.jpg)

After: <br>
![Run query and list result](./images/run-query-list-result2.jpg)
![Run query and list result](./images/run-query-list-result3.jpg)

Compile all the information following your configuration.
For the query field past the code below:

'''query
let selectedCategories = dynamic([]);
let selectedTotSev = dynamic([]);
SqlAssessment_CL
| extend asmt = parse_csv(RawData)
| where asmt[11] =~ "MSSQLSERVER" 
| extend
    AsmtId=tostring(asmt[1]),
    CheckId=tostring(asmt[2]),
    DisplayString=asmt[3],
    Description=tostring(asmt[4]),
    HelpLink=asmt[5],
    TargetType=case(asmt[6] == 1, "Server", asmt[6] == 2, "Database", ""),
    TargetName=tostring(asmt[7]), 
    Severity=case(asmt[8] == 30, "High", asmt[8] == 20, "Medium", asmt[8] == 10, "Low", asmt[8] == 0, "Information", asmt[8] == 1, "Warning", asmt[8] == 2, "Critical", "Passed"),
    Message=tostring(asmt[9]),
    TagsArr=split(tostring(asmt[10]), ","),
    Sev = toint(asmt[8])
| where isnotempty(AsmtId)  
    and (set_has_element(dynamic(['*']), CheckId) or "'*'" == "'*'")
    and (set_has_element(dynamic(['*']), TargetName) or "'*'" == "'*'")
    and set_has_element(dynamic([30, 20, 10, 0]), Sev)
    and (array_length(set_intersect(TagsArr, dynamic(['*']))) > 0 or "'*'" == "'*'")
    and (CheckId == '''' and Sev == 0 or "''" == "''")
| extend Category = case(
                        array_length(set_intersect(TagsArr, dynamic(["CPU", "IO", "Storage"]))) > 0,
                        '0',
                        array_length(set_intersect(TagsArr, dynamic(["TraceFlag", "Backup", "DBCC", "DBConfiguration", "SystemHealth", "Traces", "DBFileConfiguration", "Configuration", "Replication", "Agent", "Security", "DataIntegrity", "MaxDOP", "PageFile", "Memory", "Performance", "Statistics"]))) > 0,
                        '1',
                        array_length(set_intersect(TagsArr, dynamic(["UpdateIssues", "Index", "Naming", "Deprecated", "masterDB", "QueryOptimizer", "QueryStore", "Indexes"]))) > 0,
                        '2',
                        '3'
                    )
| where (Sev >= 0 and array_length(selectedTotSev) == 0 or Sev in (selectedTotSev))
    and (Category in (selectedCategories) or array_length(selectedCategories) == 0)
| project
    TargetType,
    TargetName,
    Severity,
    Message,
    Tags=strcat_array(array_slice(TagsArr, 1, -1), ', '),
    CheckId,
    Description,
    HelpLink = tostring(HelpLink),
    SeverityCode = toint(Sev)
| order by SeverityCode desc, TargetType desc, TargetName asc
| project-away SeverityCode'''


The first "Get blob content (V2)" block must be configured with the final name of the blob (read file) that will store the Delta URL Link (in yellow). Please Use the same Blob Name for all the Blob Blocs:
 

 
The Flow have different HTTP blocks required for get informations from Azure Graph. Please exand all the foreach blocks and customize the HTTP blocks as shown below. Keep in mind that the chosen solution must have permission to read Azure Graph. Ensure to have a Service Principal with all the required permission <a href=https://learn.microsoft.com/en-us/graph/api/user-get?>reported here</a>  and <a href=https://learn.microsoft.com/en-us/azure/purview/create-service-principal-azure>here</a>. For a tutorial please refer to <a href=https://techcommunity.microsoft.com/t5/azure-integration-services-blog/calling-graph-api-from-azure-logic-apps-using-delegated/ba-p/1997666>this link</a>:
 
<img src="https://i.ibb.co/ZTPXx8t/HTTPrequest.png" alt="HTTPrequest" title="HTTPrequest">
 
Remember to change the group name inside the foreach block in order to check only modification to the required group:
 
<img src="https://i.ibb.co/VgsHD4b/groupname.png" alt="groupname" title="groupname">
 
The last step is to add and customise the Send e-mail form (V2) to send an e-mail with the requested information. You can customise it as you wish. The block must be placed at the end of the logic app flow.
 
<img src="https://i.ibb.co/MG4GQJd/email.png" alt="email" title="email">
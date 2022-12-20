# KQL-for-Azure

Log Analytics - Azure Activity Table - Resources

The following can be used as a reference for quering into a Log Analytics workspace for AzureActivity Table

    // This shows the count of rows in the table. This depends on the time range you choose in Log Analytics
    AzureActivity
    | count
     
    // Here you are returning the rows based on the TimeGenerated
    AzureActivity
    | where TimeGenerated < datetime(2/8/2022 00:00:00)
    | count 
     
    // Here you are returning the rows based on the TimeGenerated
    AzureActivity
    | where TimeGenerated < ago(1h)
    | count 
     
    // You can also print the values to the workspace
    print(ago(1h))
     
    print(datetime(2/8/2022 00:00:00))
     
     
    // Searching for failure 
    // Free Text Search
     
    AzureActivity
    | search "Failure"
     
    AzureActivity
    | search "Failure"
    | where TimeGenerated < ago(1h)
     
    // Here you are using the where clause to filter on a specific column value
    AzureActivity
    | where ActivityStatusValue contains "Failure"
     
     
    AzureActivity
    | where ActivityStatusValue contains "Failure"
    |count
     
    // Group by ActivityStatusValue
    AzureActivity
    | summarize count() by ActivityStatusValue
     
    // You can summarize by multiple columns
    AzureActivity
    | where TimeGenerated < ago(1h)
    | summarize count() by ResourceGroup,ActivityStatusValue
    | sort by ResourceGroup
     
     
    // count - Returns the number of records in the input record set.
    // count() - Aggregation function
     
    // Grouping by buckets of TimeGenerated
    AzureActivity
    | summarize count() by bin(TimeGenerated, 1h)
    | sort by TimeGenerated
     
     
    AzureActivity
    | where TimeGenerated between (datetime(2/8/2022 03:00:00) .. datetime(2/8/2022 04:00:00))
    | count
     
     
    // Summarize operations per OperationNameValue and resource group
    AzureActivity
    | summarize count() by OperationNameValue,ResourceGroup
     
     
    // Project only certain columns
    AzureActivity
    | project OperationNameValue,ResourceGroup
     
     
    // Accessing the details of an object
    AzureActivity
    | project OperationNameValue,ResourceGroup,Properties_d["eventCategory"]
     
    // Converting string to a dynamic object
    AzureActivity
    | extend r=todynamic(HTTPRequest)
    | project OperationNameValue,ResourceGroup,r["clientIpAddress"]
     
--------------------------------------------------------------------------------------------------------------------------------------------------------

Log Analytics - Exploring other Tables - Resources


1. For the Sign-in logs table

    SigninLogs 
    | extend s=todynamic(Status) 
    | where s["errorCode"]<>0

2. For the Firewall logs in the Azure Diagnostics table


    AzureDiagnostics 
    | parse msg_s with Protocol 'request from ' *
    | extend l_protocol=Protocol
    | project l_protocol

    AzureDiagnostics 
    | parse msg_s with Protocol 'request from ' SourceIP ':' * 'to ' DestinationIP ':' *
    | extend l_protocol=Protocol,l_sourceip=SourceIP,l_destination=DestinationIP
    | project l_protocol,l_sourceip,l_destination

3. For the Security Events table

    SecurityEvent
    | where EventID ==4673
    | project Account

    SecurityEvent
    | summarize count() by bin(TimeGenerated, 1h)
    | sort by TimeGenerated

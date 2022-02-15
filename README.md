# Cloudguard CSPM webhook forwarder for Microsoft Sentinel
<br>
<br>
<p align="center">  
<img width="800" src="./images/check_point_logo.jpg"> </a>
</p>
<br>

# Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prereq)
2. [Deploy](#deploy)
3. [Test notifications from CSPM to Sentinel](#testnotifications)

<br>

<a name="overview">

# Overview

This is an Azure function app that serves as a webhook forwarder. It will allow users to send notifications from Check Point CloudGuard CSPM to Microsoft Sentinel. This repo will deploy the webhook forwarder as an Azure function app. 

<p align="left">  
<img width="400" src="./images/cp_webhook_forwarder.png"> </a>
</p>

For more information see:

[CloudGuard CSPM Notification](https://sc1.checkpoint.com/documents/CloudGuard_Dome9/Documentation/Settings/Notifications.htm?tocpath=Settings%20%7C_____4)

[Microsoft Sentinel Data Collector API](https://docs.microsoft.com/azure/sentinel/connect-rest-api-template)  

[Logic App Overview](https://azure.microsoft.com/services/logic-apps/)

<br>
<br>

<a name="prereq">

# Prerequisites

1. You must have read and write permissions on the Microsoft Sentinel workspace.

2. You must have read permissions to shared keys for the workspace.

    [Learn more about workspace keys.](https://docs.microsoft.com/en-us/azure/azure-monitor/agents/agent-windows)

<br>
<br>

<a name="Deploy">

# Deploy

1. Get Microsoft Sentinel Workspace ID and Primary Key 

    a. From Azure Portal -> Microsoft Sentinel Workspace -> Settings -> Workspace settings -> Agents management

    b. Copy Workspace ID and the Primary Key

    <p align="left">  
    <img width="400" src="./images/ms_workspace-id-primary-key.png"> </a>
    </p>

2. Launch Template

    [![Deploy to Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fchkp-jguo%2Fcloudguard_cspm_forwarder%2Fmain%2FdeployCP.json)


3. Fill template details

    <p align="left">  
    <img width="400" src="./images/ms_function_params.png"> </a>
    </p>


4. Obtain function app endpoint URL

    <p align="left">  
    <img width="400" src="./images/ms_function_url.png"> </a>
    </p>

5. Configure Cloudguard CSPM notification 

    a. Login to Check Point CloudGuard CSPM portal

    b. Settings -> Notifications -> Add Notification 
    
    c. Fill in the details
    
    d. Endpoint url - make sure to include the following at end of function URL "/api/forwarder/"
    
    e. Test endpoint
    
    f. Save notification policy

    Example: 

    <p align="left">  
    <img width="400" src="./images/cp_cspm_notification_config.png"> </a>
    </p>


6. Assign notification policy to a security bundle

    a. From CloudGuard CSPM portal

    b. Posture Management -> Continous Posture -> Select bundle
    
    c. Edit bundle notiifcation policy 

    <p align="left">  
    <img width="400" src="./images/cp_cspm_edit_policy.png"> </a>
    </p>

    d. Select notification policy and Save settings

    <p align="left">  
    <img width="400" src="./images/cp_cspm_set_notification_policy.png"> </a>
    </p>

# Test notifications

1. From CloudGuard CSPM portal

2. Posture Management -> Continous Posture -> Select bundle   

3. Select "Send all alerts"
    <p align="left">  
    <img width="400" src="./images/cp_cspm_send_all_alerts.png"> </a>
    </p>

4. Select notification policy and Send
    <p align="left">  
    <img width="400" src="./images/cp_cspm_send_all_alerts_pol.png"> </a>
    </p>

5. From Microsoft Sentinel Workspace -> Select CloudguardCSPM_CL or user specified log_type name for CloudGuard CSPM. 

    <p align="left">  
    <img width="400" src="./images/ms_sentinel_cloudguard_cspm.png"> </a>
    </p>

    Note: Microsoft Sentinel Data Collector API appends "_CL" to all ingested data fields. 

6. Filter events

    a. Filter by: 

        - rule_severity_s
        - rule_ruleId_s
        - rule_name_s
        - entity_region_s
        - entity_name_s
        - rule_description_s
        - rule_remediation_s
        - entity_id_s

    example: 

    <p align="left">  
    <img width="600" src="./images/ms_sentinel_query.png"> </a>
    </p>
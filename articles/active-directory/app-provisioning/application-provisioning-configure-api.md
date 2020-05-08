---
title: 使用 Microsoft Graph Api 配置预配-Azure Active Directory |Microsoft Docs
description: 需要为应用程序的多个实例设置预配？ 了解如何通过使用 Microsoft Graph Api 来自动完成自动预配配置来节省时间。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 585cafc548b3458c6e9cc0ef91c44f163fb7fa2f
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593941"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>使用 Microsoft Graph Api 配置预配

Azure 门户是一种简单的方法，用于一次配置单个应用的预配。 但是，如果您要创建应用程序的多个实例，甚至是数百个，则使用 Microsoft Graph Api 来自动创建和配置应用程序会更容易。 本文概述了如何通过 Api 自动预配配置。 此方法通常用于[Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso)的应用程序。

**使用 Microsoft Graph Api 自动执行预配配置的步骤概述**


|步骤  |详细信息  |
|---------|---------|
|[步骤1。创建库应用程序](#step-1-create-the-gallery-application)     |登录到 API 客户端 <br> 检索库应用程序模板 <br> 创建库应用程序         |
|[步骤2。基于模板创建预配作业](#step-2-create-the-provisioning-job-based-on-the-template)     |检索预配连接器的模板 <br> 创建设置作业         |
|[步骤3。授权访问](#step-3-authorize-access)     |测试与应用程序的连接 <br> 保存凭据         |
|[步骤4。开始预配作业](#step-4-start-the-provisioning-job)     |启动作业         |
|[步骤5。监视预配](#step-5-monitor-provisioning)     |检查设置作业的状态 <br> 检索预配日志         |

> [!NOTE]
> 为了便于阅读，本文中显示的响应对象可能会缩短。 所有属性都将从实际调用返回。

## <a name="step-1-create-the-gallery-application"></a>步骤1：创建库应用程序

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>登录到 Microsoft Graph 资源管理器（推荐）、Postman 或使用的任何其他 API 客户端

1. 启动[Microsoft Graph 资源管理器](https://developer.microsoft.com/graph/graph-explorer)
1. 选择 "使用 Microsoft 登录" 按钮，并使用 Azure AD 全局管理员或应用管理员凭据进行登录。

    ![Graph 登录](./media/application-provisioning-configure-api/wd_export_02.png)

1. 成功登录后，左侧窗格中会显示用户帐户的详细信息。

### <a name="retrieve-the-gallery-application-template-identifier"></a>检索库应用程序模板标识符
每个应用程序库中 Azure AD 的应用程序都有一个描述该应用程序的元数据的[应用程序模板](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http)。 使用此模板，可以在租户中创建应用程序和服务主体的实例，以进行管理。

#### <a name="request"></a>*请求*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*响应*

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>创建库应用程序

在上一步中，使用为应用程序检索的模板 ID 在租户中创建应用程序和服务主体的[实例](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)。

#### <a name="request"></a>*请求*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*响应*


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>步骤2：基于模板创建设置作业

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>检索预配连接器的模板

库中为预配启用的应用程序具有可简化配置的模板。 使用以下请求[检索预配配置的模板](https://docs.microsoft.com/graph/api/synchronization-synchronizationtemplate-list?view=graph-rest-beta&tabs=http)。 请注意，你将需要提供 ID。 ID 是指前面的资源，在本例中为 ServicePrincipal。 

#### <a name="request"></a>*请求*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*响应*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>创建设置作业
若要启用预配，首先需要[创建一个作业](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http)。 使用以下请求创建设置作业。 指定要用于作业的模板时，请使用上一步中的 templateId。

#### <a name="request"></a>*请求*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*响应*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>步骤3：授予访问权限

### <a name="test-the-connection-to-the-application"></a>测试与应用程序的连接

测试与第三方应用程序的连接。 以下示例适用于需要 clientSecret 和 secretToken 的应用程序。 每个应用程序都有自己的要求。 应用程序通常使用 BaseAddress 来代替 ClientSecret。 若要确定应用所需的凭据，请导航到应用程序的 "设置配置" 页，并在 "开发人员模式" 中单击 "测试连接"。 网络流量将显示用于凭据的参数。 可在[此处](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http)找到完整的凭据列表。 

#### <a name="request"></a>*请求*
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*响应*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>保存凭据

配置预配要求在 Azure AD 与应用程序之间建立信任。 授予对第三方应用程序的访问权限。 以下示例适用于需要 clientSecret 和 secretToken 的应用程序。 每个应用程序都有自己的要求。 查看[API 文档](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http)以查看可用选项。 

#### <a name="request"></a>*请求*
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*响应*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>步骤4：启动设置作业
配置预配作业后，请使用以下命令[启动作业](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http)。 


#### <a name="request"></a>*请求*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*响应*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>步骤5：监视预配

### <a name="monitor-the-provisioning-job-status"></a>监视预配作业状态

现在预配作业正在运行，请使用以下命令来跟踪当前预配周期的进度以及统计信息，例如目标系统中已创建的用户和组的数目。 

#### <a name="request"></a>*请求*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*响应*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>使用预配日志监视预配事件
除了监视预配作业的状态之外，还可以使用[设置日志](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http)来查询发生的所有事件（例如，查询特定用户并确定是否已成功设置这些事件）。

#### <a name="request"></a>*请求*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*响应*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>相关文章

- [查看同步 Microsoft Graph 文档](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [将自定义 SCIM 应用与 Azure AD 集成](use-scim-to-provision-users-and-groups.md)

---
title: "逻辑应用中的日志记录和错误处理 | Microsoft Docs"
description: "使用逻辑应用查看高级错误处理和日志记录的实际用例"
keywords: 
services: logic-apps
author: hedidin
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: b-hoedid
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4d211ab7f34017f7fcfd6150b916a038e7391c51


---
# <a name="logging-and-error-handling-in-logic-apps"></a>逻辑应用中的日志记录和错误处理
本指南介绍如何扩展逻辑应用以更好地支持异常处理。 这是实际用例，以及我们对问题“逻辑应用是否支持异常和错误处理？”的回答

> [!NOTE]
> Microsoft Azure App Service 的逻辑应用功能的当前版本为操作响应提供标准模板。
> 这包括内部验证以及从 API 应用返回的错误响应。
> 
> 

## <a name="overview-of-the-use-case-and-scenario"></a>用例和方案的概述
下列案例是本文的使用案例。
一个著名的医疗保健组织与我们合作开发一个使用 Microsoft Dynamics CRM Online 创建患者门户的 Azure 解决方案。 他们需要在 Dynamics CRM Online 患者门户与 Salesforce 之间发送约会记录。  我们需要对所有患者记录使用 [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) 标准。

该项目具有两个主要要求：  

* 用于对从 Dynamics CRM Online 门户发送的记录进行日志记录的一种方法
* 用于查看工作流中发生的任何错误的一种方式

## <a name="how-we-solved-the-problem"></a>我们如何解决问题
> [!TIP]
> 可在[集成用户组](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Integration User Group")中查看项目的高级视频。
> 
> 

我们选择 [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/ "Azure DocumentDB") 作为日志和错误记录的存储库（DocumentDB 将记录作为文档来引用）。 由于逻辑应用具有用于所有响应的标准模板，因此我们不必创建自定义架构。 我们可以创建 API 应用以便对错误和日志记录进行**插入**和**查询**。 我们还可以在 API 应用中为各个操作定义架构。  

另一个要求是清除特定日期之后的记录。 DocumentDB 具有一个名为 [生存时间](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "生存时间") (TTL) 的属性，通过它可以为每个记录或集合设置“生存时间”值。 这样便无需在 DocumentDB 中手动删除记录。

### <a name="creation-of-the-logic-app"></a>逻辑应用的创建
第一步是在设计器中创建逻辑应用并进行加载。 在此示例中，我们使用父-子逻辑应用。 我们假设已创建了父级并将创建一个子逻辑应用。

因为我们要对从 Dynamics CRM Online 传出的记录进行日志记录，所以我们从顶部开始。 我们需要使用请求触发器，因为父逻辑应用会触发此子级。

> [!IMPORTANT]
> 若要完成本教程，需要创建一个 DocumentDB 数据库和两个集合（日志记录和错误）。
> 
> 

### <a name="logic-app-trigger"></a>逻辑应用触发器
我们使用如下面示例中所示的请求触发器。

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


### <a name="steps"></a>步骤
我们需要对来自 Dynamics CRM Online 门户的患者记录的源（请求）进行日志记录。

1. 我们需要从 Dynamics CRM Online 获取新的约会记录。
    来自 CRM 的触发器为我们提供 **CRM PatentId**、**记录类型**、**新的或更新的记录**（新的或更新的布尔值）以及 **SalesforceId**。 **SalesforceId** 可以为 null，因为它只用于更新。
    我们使用 CRM **PatientID** 和**记录类型**来获取 CRM 记录。
2. 接下来，我们需要添加 DocumentDB API 应用 **InsertLogEntry** 操作，如以下各图所示。

#### <a name="insert-log-entry-designer-view"></a>插入日志条目设计器视图
![插入日志条目](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### <a name="insert-error-entry-designer-view"></a>插入错误条目设计器视图
![插入日志条目](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### <a name="check-for-create-record-failure"></a>检查是否存在创建记录失败
![条件](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>逻辑应用源代码
> [!NOTE]
> 以下只是示例。 由于本教程基于当前在生产中的实现，因此“源节点”的值可能不会显示与安排约会相关的属性。
> 
> 

### <a name="logging"></a>日志记录
以下逻辑应用代码示例演示如何处理日志记录。

#### <a name="log-entry"></a>日志条目
这是用于插入日志条目的逻辑应用源代码。

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>日志请求
这是发布到 API 应用的日志请求消息。

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{\"Pragma\":\"no-cache\",\"x-ms-request-id\":\"e750c9a9-bd48-44c4-bbba-1688b6f8a132\",\"OData-Version\":\"4.0\",\"Cache-Control\":\"no-cache\",\"Date\":\"Fri, 10 Jun 2016 22:31:56 GMT\",\"Set-Cookie\":\"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1\",\"Server\":\"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0\",\"X-AspNet-Version\":\"4.0.30319\",\"X-Powered-By\":\"ASP.NET\",\"Content-Length\":\"1935\",\"Content-Type\":\"application/json; odata.metadata=minimal; odata.streaming=true\",\"Expires\":\"-1\"}"
        }
    }

```


#### <a name="log-response"></a>日志响应
这是来自 API 应用的日志响应消息。

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "\"0400fc2f-0000-0000-0000-575b3ff00000\"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{\"Pragma\":\"no-cache\",\"x-ms-request-id\":\"e750c9a9-bd48-44c4-bbba-1688b6f8a132\",\"OData-Version\":\"4.0\",\"Cache-Control\":\"no-cache\",\"Date\":\"Fri, 10 Jun 2016 22:31:56 GMT\",\"Set-Cookie\":\"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1\",\"Server\":\"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0\",\"X-AspNet-Version\":\"4.0.30319\",\"X-Powered-By\":\"ASP.NET\",\"Content-Length\":\"1935\",\"Content-Type\":\"application/json; odata.metadata=minimal; odata.streaming=true\",\"Expires\":\"-1\"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

现在我们来看一下错误处理步骤。

### <a name="error-handling"></a>错误处理。
以下逻辑应用代码示例演示如何实现错误处理。

#### <a name="create-error-record"></a>创建错误记录
这是用于创建错误记录的逻辑应用源代码。

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-documentdb--request"></a>将错误插入到 DocumentDB 中 - 请求
``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{\"Account_Class_vod__c\":\"PRAC\",\"Account_Status_MED__c\":\"I\",\"CRM_HUB_ID__c\":\"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0\",\"Credentials_vod__c\",\"DTC_ID_MED__c\":\"\",\"Fax\":\"\",\"FirstName\":\"A\",\"Gender_vod__c\":\"\",\"IMS_ID__c\":\"\",\"LastName\":\"BAILEY\",\"MasterID_mp__c\":\"\",\"C_ID_MED__c\":\"851588\",\"Middle_vod__c\":\"\",\"NPI_vod__c\":\"\",\"PDRP_MED__c\":false,\"PersonDoNotCall\":false,\"PersonEmail\":\"\",\"PersonHasOptedOutOfEmail\":false,\"PersonHasOptedOutOfFax\":false,\"PersonMobilePhone\":\"\",\"Phone\":\"\",\"Practicing_Specialty__c\":\"FM - FAMILY MEDICINE\",\"Primary_City__c\":\"\",\"Primary_State__c\":\"\",\"Primary_Street_Line2__c\":\"\",\"Primary_Street__c\":\"\",\"Primary_Zip__c\":\"\",\"RecordTypeId\":\"012U0000000JaPWIA0\",\"Request_Date__c\":\"2016-06-10T22:31:55.9647467Z\",\"ONY_ID__c\":\"\",\"Specialty_1_vod__c\":\"\",\"Suffix_vod__c\":\"\",\"Website\":\"\"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-documentdb--response"></a>将错误插入到 DocumentDB 中 - 响应
``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "\"0c00eaac-0000-0000-0000-575b3fdc0000\"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{\"Account_Class_vod__c\":\"PRAC\",\"Account_Status_MED__c\":\"I\",\"CRM_HUB_ID__c\":\"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0\",\"Credentials_vod__c\":\"DO - Degree level is DO\",\"DTC_ID_MED__c\":\"\",\"Fax\":\"\",\"FirstName\":\"A\",\"Gender_vod__c\":\"\",\"IMS_ID__c\":\"\",\"LastName\":\"BAILEY\",\"MterID_mp__c\":\"\",\"Medicis_ID_MED__c\":\"851588\",\"Middle_vod__c\":\"\",\"NPI_vod__c\":\"\",\"PDRP_MED__c\":false,\"PersonDoNotCall\":false,\"PersonEmail\":\"\",\"PersonHasOptedOutOfEmail\":false,\"PersonHasOptedOutOfFax\":false,\"PersonMobilePhone\":\"\",\"Phone\":\"\",\"Practicing_Specialty__c\":\"FM - FAMILY MEDICINE\",\"Primary_City__c\":\"\",\"Primary_State__c\":\"\",\"Primary_Street_Line2__c\":\"\",\"Primary_Street__c\":\"\",\"Primary_Zip__c\":\"\",\"RecordTypeId\":\"012U0000000JaPWIA0\",\"Request_Date__c\":\"2016-06-10T22:31:55.9647467Z\",\"XXXXXXX\":\"\",\"Specialty_1_vod__c\":\"\",\"Suffix_vod__c\":\"\",\"Website\":\"\"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Salesforce 错误响应
``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="returning-the-response-back-to-the-parent-logic-app"></a>将响应返回给父逻辑应用
获取响应之后，可以将它传递回父逻辑应用。

#### <a name="return-success-response-to-the-parent-logic-app"></a>将成功响应返回给父逻辑应用
``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-the-parent-logic-app"></a>将错误响应返回给父逻辑应用
``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="documentdb-repository-and-portal"></a>DocumentDB 存储库和门户
我们的解决方案使用 [DocumentDB](https://azure.microsoft.com/services/documentdb) 添加了其他功能。

### <a name="error-management-portal"></a>错误管理门户
若要查看这些错误，可以创建 MVC web 应用，以显示来自 DocumentDB 的错误记录。 **列表**、**详细信息**、**编辑**和**删除**操作包含在当前版本中。

> [!NOTE]
> 编辑操作：DocumentDB 对整个文档进行替换。
> **列表**和**详细信息**视图中显示的记录只是示例。 它们不是实际的患者约会记录。
> 
> 

以下是我们使用前面所述的方法创建的 MVC 应用详细信息的示例。

#### <a name="error-management-list"></a>错误管理列表
![错误列表](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>错误管理详细信息视图
![错误详细信息](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>日志管理门户
为了查看日志，我们也创建了一个 MVC web 应用。  以下是我们使用前面所述的方法创建的 MVC 应用详细信息的示例。

#### <a name="sample-log-detail-view"></a>示例日志详细信息视图
![日志详细信息视图](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>API 应用详细信息
#### <a name="logic-apps-exception-management-api"></a>逻辑应用异常管理 API
我们的开源逻辑应用异常管理 API 应用提供了以下功能。

有两个控制器：

* **ErrorController** 将错误记录（文档）插入 DocumentDB 集合中。
* **LogController** 将日志记录（文档）插入 DocumentDB 集合中。

> [!TIP]
> 这两个控制器都使用 `async Task<dynamic>` 操作。 这样可以在运行时解析操作，因此我们可以在操作的正文中创建 DocumentDB 架构。
> 
> 

DocumentDB 中的每个文档都必须具有唯一 ID。 我们使用 `PatientId` 并添加戳转换为 Unix 时间戳值（双精度型）的时间戳。 我们将它截断以删除小数值。

可以[从 GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs) 查看我们的错误控制器 API 的源代码。

我们使用以下语法从逻辑应用调用该 API。

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

前面代码示例中的表达式在检查 Create_NewPatientRecord 的状态是否为 **Failed**。

## <a name="summary"></a>摘要
* 可以在逻辑应用中轻松实现日志记录和错误处理。
* 可以使用 DocumentDB 作为日志和错误记录的存储库（文档）。
* 可以使用 MVC 创建门户来显示日志和错误记录。

### <a name="source-code"></a>源代码
逻辑应用异常管理 API 应用程序的源代码位于此 [GitHub 存储库](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "逻辑应用异常管理 API")中。

## <a name="next-steps"></a>后续步骤
* [查看更多逻辑应用示例和方案](app-service-logic-examples-and-scenarios.md)
* [了解逻辑应用监视工具](app-service-logic-monitor-your-logic-apps.md)
* [创建逻辑应用自动部署模板](app-service-logic-create-deploy-template.md)




<!--HONumber=Nov16_HO3-->



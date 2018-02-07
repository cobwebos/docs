---
title: "Azure 数据工厂中的 Web 活动 | Microsoft Docs"
description: "了解如何使用 Web 活动（数据工厂支持的控制流活动之一）从管道调用 REST 终结点。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 04b542bf1f77b75c1c92b147b578df630b86d0ac
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="web-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Web 活动
Web 活动可用于从数据工厂管道调用自定义的 REST 终结点。 可以传递数据集和链接服务以供活动使用和访问。 

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 文档](v1/data-factory-introduction.md)。

## <a name="syntax"></a>语法

```json
{  
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{  
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{  
         "Content-Type":"application/json"
      },
      "authentication":{  
         "type":"ClientCertificate",  
         "pfx":"****",
         "password":"****"
      },
      "datasets":[  
         {  
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{  
               ...
            }
         }
      ],
      "linkedServices":[  
         {  
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | --------
name | Web 活动的名称 | String | 是
type | 必须设置为 **WebActivity**。 | String | 是
方法 | 目标终结点的 Rest API 方法。 | 字符串。 <br/><br/>支持的类型：“GET”、“POST”、“PUT” | 是
url | 目标终结点和路径 | 字符串（或带有 resultType 字符串的表达式） | 是
headers | 发送到请求的标头。 例如，若要在请求中设置语言和类型：`"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`。 | 字符串（或带有 resultType 字符串的表达式） | 是，需要内容类型标头。 `"headers":{ "Content-Type":"application/json"}`
body | 表示要发送到终结点的有效负载。 POST/PUT 方法所必需。  | 字符串（或带有 resultType 字符串的表达式）。 <br/><br/>请参阅[请求有效负载架构](#request-payload-schema)部分中的请求有效负载架构。 | 否
authentication | 用于调用该终结点的身份验证方法。 支持的类型为“Basic”或“ClientCertificate”。 有关详细信息，请参阅[身份验证](#authentication)部分。 如果不需要身份验证，则排除此属性。 | 字符串（或带有 resultType 字符串的表达式） | 否
datasets | 传递给终结点的数据集列表。 | 数据集引用数组。 可以是空数组。 | 是
linkedServices | 传递给终结点的链接服务列表。 | 链接服务引用数组。 可以是空数组。 | 是

> [!NOTE]
> Web 活动调用的 REST 终结点必须返回 JSON 类型的响应。

## <a name="authentication"></a>身份验证

### <a name="none"></a>无
如果不需要身份验证，请排除“身份验证”属性。

### <a name="basic"></a>基本
指定用户名和密码以用于基本身份验证。 

```json
"authentication":{  
   "type":"Basic,
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>客户端证书
指定 base64 编码的 PFX 文件内容和密码。 

```json
"authentication":{  
   "type":"ClientCertificate",
   "pfx":"****",   
   "password":"****"
}
```
## <a name="request-payload-schema"></a>请求有效负载架构
当使用 POST/PUT 方法时，正文属性表示发送到终结点的有效负载。 可以将链接服务和数据集作为有效负载的一部分进行传递。 下面是有效负载的架构： 

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
} 
```

## <a name="example"></a>示例
在此示例中，管道中的 Web 活动调用了 REST 终结点。 它将 Azure SQL 链接服务和 Azure SQL 数据集传递到该终结点。 REST 终结点使用 Azure SQL 连接字符串连接到 Azure SQL Server，并返回 SQL Server 实例的名称。 

### <a name="pipeline-definition"></a>管道定义

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>管道参数值

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Web 服务终结点代码

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)

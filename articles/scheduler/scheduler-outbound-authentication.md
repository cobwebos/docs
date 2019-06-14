---
title: 出站身份验证 - Azure 计划程序
description: 了解如何设置或删除 Azure 计划程序的出站身份验证
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 42d6ec93a3382f494b49fb574c4aee5e8eec142a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708940"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Azure 计划程序的出站身份验证

> [!IMPORTANT]
> [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)将替换即将停用的 Azure 计划程序。 若要计划作业，请[改为试用 Azure 逻辑应用](../scheduler/migrate-from-scheduler-to-logic-apps.md)。 

Azure 计划程序作业必须调用需要进行身份验证的服务，例如其他 Azure 服务、Salesforce.com、Facebook 和安全自定义网站。 被调用的服务可以确定计划程序作业是否可以访问请求的资源。 

计划程序支持以下身份验证模型： 

* 使用 SSL/TLS 客户端证书时的客户端证书  身份验证
* 基本身份验证 
* Active Directory OAuth  身份验证

## <a name="add-or-remove-authentication"></a>添加或删除身份验证

* 若要向计划程序作业添加身份验证，请在创建或更新作业时，将 `authentication` JavaScript 对象表示法 (JSON) 子元素添加到 `request` 元素。 

  通过 `authentication` 对象中的 PUT、PATCH 或 POST 请求中传递给计划程序服务的机密永远不会在响应中返回。 
  响应将机密信息设置为 null，或者使用表示已经过身份验证的实体的公共令牌。 

* 若要从计划程序作业中删除身份验证，请在作业上显式运行 PUT 或 PATCH 请求，并将 `authentication` 对象设置为 null。 响应将不包含任何身份验证属性。

## <a name="client-certificate"></a>客户端证书

### <a name="request-body---client-certificate"></a>请求正文 - 客户端证书

使用 `ClientCertificate` 模型添加身份验证时，请在请求正文中指定以下附加元素。  

| 元素 | 需要 | 描述 |
|---------|----------|-------------|
| authentication（父元素）  | 用于使用 SSL 客户端证书的身份验证对象 |
| **type** | 是 | 身份验证类型。 对于 SSL 客户端证书，该值为 `ClientCertificate`。 |
| **pfx** | 是 | PFX 文件的 base64 编码内容 |
| **password** | 是 | 用于访问 PFX 文件的密码 |
||| 

### <a name="response-body---client-certificate"></a>响应正文 - 客户端证书 

发送包含身份验证信息的请求时，响应将包含以下身份验证元素。

| 元素 | 描述 | 
|---------|-------------| 
| authentication（父元素）  | 用于使用 SSL 客户端证书的身份验证对象 |
| **type** | 身份验证类型。 对于 SSL 客户端证书，该值为 `ClientCertificate`。 |
| **certificateThumbprint** |证书的指纹 |
| **certificateSubjectName** |证书使用者可分辨名称 |
| **certificateExpiration** | 证书的过期日期 |
||| 

### <a name="sample-rest-request---client-certificate"></a>示例 REST 请求 - 客户端证书

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>示例 REST 响应 - 客户端证书

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>基本

### <a name="request-body---basic"></a>请求正文 - 基本

使用 `Basic` 模型添加身份验证时，请在请求正文中指定以下附加元素。

| 元素 | 需要 | 描述 |
|---------|----------|-------------|
| authentication（父元素）  | 用于使用基本身份验证的身份验证对象 | 
| **type** | 是 | 身份验证类型。 对于基本身份验证，该值为 `Basic`。 | 
| **username** | 是 | 要进行身份验证的用户名 | 
| **password** | 是 | 要进行身份验证的密码 |
|||| 

### <a name="response-body---basic"></a>响应正文 - 基本

发送包含身份验证信息的请求时，响应将包含以下身份验证元素。

| 元素 | 描述 | 
|---------|-------------|
| authentication（父元素）  | 用于使用基本身份验证的身份验证对象 |
| **type** | 身份验证类型。 对于基本身份验证，该值为 `Basic`。 |
| **username** | 经过身份验证的用户名 |
||| 

### <a name="sample-rest-request---basic"></a>示例 REST 请求 - 基本

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>示例 REST 响应 - 基本

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>请求正文 - Active Directory OAuth 

使用 `ActiveDirectoryOAuth` 模型添加身份验证时，请在请求正文中指定以下附加元素。

| 元素 | 需要 | 描述 |
|---------|----------|-------------|
| authentication（父元素）  | 是 | 用于使用 ActiveDirectoryOAuth 身份验证的身份验证对象 |
| **type** | 是 | 身份验证类型。 对于 ActiveDirectoryOAuth 身份验证，该值为 `ActiveDirectoryOAuth`。 |
| **tenant** | 是 | Azure AD 租户的租户标识符。 若要找到 Azure AD 租户的租户标识符，请在 Azure PowerShell 中运行 `Get-AzureAccount`。 |
| **audience** | 是 | 此值设置为 `https://management.core.windows.net/`。 | 
| **clientId** | 是 | Azure AD 应用程序的客户端标识符 | 
| **secret** | 是 | 正在请求令牌的客户端的机密 | 
|||| 

### <a name="response-body---active-directory-oauth"></a>响应正文 - Active Directory OAuth

发送包含身份验证信息的请求时，响应将包含以下身份验证元素。

| 元素 | 描述 |
|---------|-------------|
| authentication（父元素）  | 用于使用 ActiveDirectoryOAuth 身份验证的身份验证对象 |
| **type** | 身份验证类型。 对于 ActiveDirectoryOAuth 身份验证，该值为 `ActiveDirectoryOAuth`。 | 
| **tenant** | Azure AD 租户的租户标识符 |
| **audience** | 此值设置为 `https://management.core.windows.net/`。 |
| **clientId** | Azure AD 应用程序的客户端标识符 |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>示例 REST 请求 - Active Directory OAuth

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>示例 REST 响应 - Active Directory OAuth

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="see-also"></a>另请参阅

* [什么是 Azure 计划程序？](scheduler-intro.md)
* [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)
* [Azure 计划程序的限制、默认值和错误代码](scheduler-limits-defaults-errors.md)
* [Azure 计划程序 REST API](https://msdn.microsoft.com/library/mt629143)
* [Azure 计划程序 PowerShell cmdlet 参考](scheduler-powershell-reference.md)

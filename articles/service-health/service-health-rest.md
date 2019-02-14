---
title: 使用 REST API 获取 Azure 资源运行状况事件 | Microsoft Docs
description: 使用 Azure REST API 以获取 Azure 资源的运行状况事件。
author: stephbaron
ms.author: stbaron
ms.service: service-health
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.openlocfilehash: 6d83aed6910127ceb34b9a694f48ca9c19ab6d18
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878385"
---
# <a name="get-resource-health-using-the-rest-api"></a>使用 REST API 获取资源运行状况 

此示例文章演示如何使用 [Azure REST API](/rest/api/azure/) 检索订阅中 Azure 资源的运行状况事件列表。

[Azure Monitor REST 参考](/rest/api/monitor)中提供了完整的参考文档和 REST API 的其他示例。 

## <a name="build-the-request"></a>生成请求

使用以下 `GET` HTTP 请求列出 `2018-05-16` 和 `2018-06-20` 之间的时间范围内订阅的运行状况事件。

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>请求标头

以下标头是必需的： 

|请求标头|说明|  
|--------------------|-----------------|  
|Content-Type：|必需。 设置为 `application/json`。|  
|Authorization：|必需。 设置为有效的 `Bearer` [访问令牌](/rest/api/azure/#authorization-code-grant-interactive-clients)。 |  

### <a name="uri-parameters"></a>URI 参数

| Name | 说明 |
| :--- | :---------- |
| subscriptionId | 用于标识 Azure 订阅的订阅 ID。 如果拥有多个订阅，请参阅[使用多个订阅](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。 |
| api-version | 要用于请求的 API 版本。<br /><br /> 本文档涵盖 API 版本 `2015-04-01`，包含于上述 URL 中。  |
| $filter | 用于减少返回的结果集的筛选选项。 此参数的允许模式在[活动日志操作参考](/rest/api/monitor/activitylogs/list#uri-parameters)中可用。 所示的示例捕获 2018-05-16 和 2018-06-20 之间的时间范围内的所有事件 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>请求正文

此操作无需任何请求正文。

## <a name="handle-the-response"></a>处理响应

返回状态代码 200，其中包含与筛选器参数相对应的运行状况事件值，以及用于检索下一页结果的 `nextlink` URI。

## <a name="example-response"></a>示例响应 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

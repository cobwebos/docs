---
title: "提供程序资源使用情况 API |Microsoft 文档"
description: "针对资源使用情况 API 的引用，即在检索 Azure 堆栈使用情况信息"
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: b6055923-b6a6-45f0-8979-225b713150ae
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: alfredop
ms.openlocfilehash: 0c45ce3bc93945ed8700464beebabcda07e8d77c
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="provider-resource-usage-api"></a>提供商资源使用情况 API
术语*提供程序*适用于任何委派的提供程序和服务管理员。 Azure 堆栈运算符和委派的提供程序可以使用提供程序使用情况 API，查看其直接租户的使用情况。 例如，P0 关系图中所示，可以调用提供程序 API，以获取有关 P1 的使用情况信息和 P2 的直接使用和 P1 都可以调用有关 P3 和 P4 使用情况信息。

![概念模型的提供程序层次结构](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>API 调用参考
### <a name="request"></a>请求
请求获取消耗详细信息的请求订阅和请求的时间范围。 没有任何请求正文。

这种用法 API 是提供程序 API，因此必须将调用方分配的所有者、 参与者或读取器的角色，提供程序的订阅中。

| **方法** | **请求 URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId = {sub1.1} （& a) 的 api-version = 2015年-06-01-预览版 （& a) continuationToken = {令牌 value} |

### <a name="arguments"></a>参数
| **自变量** | **说明** |
| --- | --- |
| *armendpoint* |Azure 堆栈环境的 azure 资源管理器终结点。 Azure 堆栈约定是 Azure 资源管理器终结点的名称采用以下格式`https://adminmanagement.{domain-name}`。 例如，对于开发工具包，如果域名为*local.azurestack.external*，则资源管理器终结点是`https://adminmanagement.local.azurestack.external`。 |
| *subId* |进行调用的用户的订阅 ID。 |
| *reportedStartTime* |查询的开始时间。 值*DateTime*应为以协调世界时 (UTC) 和开头的小时，例如，13:00。 为每日聚合，将此值设置为 UTC 午夜。 格式是*转义*ISO 8601。 例如， *2015年-06-16t18%3a53%3a11%2b00 %3a00z*，其中的冒号转义到*%3a*和加号转义到*%2b* ，以便它适合 URI。 |
| *reportedEndTime* |查询的结束时间。 将应用于的约束*reportedStartTime*也适用于此自变量。 值*reportedEndTime*不能为将来或当前日期。 如果是，结果设置为"正在处理未完成。" |
| *aggregationGranularity* |有两个离散潜在值的可选参数： 每天和每小时。 建议值，一个在日粒度返回的数据，另一个是每小时解析度。 默认值为每天选项。 |
| *subscriberId* |订阅 ID。 若要获取筛选后的数据，提供程序直接租户的订阅 ID 是必需的。 如果未不指定任何订阅 ID 参数，调用将返回提供程序的所有直接租户的使用情况数据。 |
| *api 版本* |用于发出此请求的协议的版本。 此值设置为*2015年-06-01-预览版*。 |
| *continuationToken* |从使用情况 API 提供程序的最后一个调用中检索令牌。 当响应是大于 1,000 行，它充当进度的书签，才需要此令牌。 如果该令牌不存在，从在一天开始检索的数据或小时，基于粒度传入。 |

### <a name="response"></a>响应
获取 /subscriptions/sub1/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 和 reportedEndTime = 2015年-06-01t00%3a00%3a00%2b00 %3a00 （& a)aggregationGranularity = 每日 subscriberId = sub1.1 的 api-version = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>响应详细信息
| **自变量** | **说明** |
| --- | --- |
| *id* |使用聚合的唯一 ID。 |
| *name* |使用聚合的名称。 |
| *类型* |资源定义。 |
| *subscriptionId* |Azure 堆栈用户的订阅标识符。 |
| *usageStartTime* |Utc 格式的开始时间为此使用聚合所属的使用情况存储桶。|
| *usageEndTime* |为此使用聚合所属的使用情况存储桶的 UTC 结束时间。 |
| *instanceData* |实例 （采用新格式） 的详细信息的键 / 值对：<br> *resourceUri*： 完全限定的资源 ID，其中包括资源组和实例名称。 <br> *位置*： 在其中运行此服务的区域。 <br> *标记*： 由用户指定的资源标记。 <br> *additionalInfo*： 更多有关的详细信息已耗用，例如的资源的 OS 版本或图像类型。 |
| *数量* |这段时间内发生的资源消耗的量。 |
| *meterId* |已耗用的资源的唯一 ID (也称为*ResourceID*)。 |

## <a name="next-steps"></a>后续步骤
[租户资源使用情况 API 参考](azure-stack-tenant-resource-usage-api.md)

[使用情况相关的常见问题](azure-stack-usage-related-faq.md)

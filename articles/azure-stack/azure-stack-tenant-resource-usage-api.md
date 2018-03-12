---
title: "租户资源使用情况 API |Microsoft 文档"
description: "针对资源使用情况 API 的引用，即在检索 Azure 堆栈使用情况信息。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: b9d7c7ee-e906-4978-92a3-a2c52df16c36
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: bc0b9993119342f07c28ed0384c11ae0f15bc439
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="tenant-resource-usage-api"></a>租户资源使用情况 API

租户可用于租户 API 查看租户的资源使用情况数据。 此 API 已与 Azure 使用情况 API 保持一致 （目前处于特邀预览阶段）。

可以使用 Windows PowerShell cmdlet **Get UsageAggregates**若要在 Azure 中获取类似的使用情况数据。

## <a name="api-call"></a>API 调用
### <a name="request"></a>请求
请求获取消耗详细信息的请求订阅和请求的时间范围。 没有任何请求正文。

| **方法** | **请求 URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>参数
| **自变量** | **说明** |
| --- | --- |
| *armendpoint* |Azure 堆栈环境的 azure 资源管理器终结点。 Azure 堆栈约定是 Azure 资源管理器终结点的名称采用以下格式`https://management.{domain-name}`。 例如，对于开发工具包中，域名称是 local.azurestack.external，则资源管理器终结点是`https://management.local.azurestack.external`。 |
| *subId* |进行调用的用户的订阅 ID。 仅对查询此 API 可用于单个订阅的使用情况。 提供程序可用于所有租户提供程序资源使用情况 API 对查询使用情况。 |
| *reportedStartTime* |查询的开始时间。 值*DateTime*应在 UTC 并且小时，例如，13:00 的开头。 为每日聚合，将此值设置为 UTC 午夜。 格式是*转义*ISO 8601，例如，2015年-06-16t18%3a53%3a11%2b00 %3a00z，其中将冒号转义为 %3a 和加上转义为 %2b，以便它适合 URI。 |
| *reportedEndTime* |查询的结束时间。 将应用于的约束*reportedStartTime*也适用于此自变量。 值*reportedEndTime*不能为将来。 |
| *aggregationGranularity* |有两个离散潜在值的可选参数： 每天和每小时。 建议值，一个在日粒度返回的数据，另一个是每小时解析度。 默认值为每天选项。 |
| *api-version* |用于发出此请求的协议的版本。 你必须使用 2015年-06-01-预览版。 |
| *continuationToken* |从使用情况 API 提供程序的最后一个调用中检索令牌。 当响应是大于 1,000 行，它充当进度的书签，才需要此令牌。 如果不存在，请从在一天开始检索的数据或小时，基于粒度传入。 |

### <a name="response"></a>响应
GET /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
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
| *id* |使用聚合的唯一 ID |
| *name* |使用聚合的名称 |
| *类型* |资源定义 |
| *subscriptionId* |Azure 用户的订阅标识符 |
| *usageStartTime* |Utc 格式的开始时间为此使用聚合所属的使用情况存储桶 |
| *usageEndTime* |为此使用聚合所属的使用情况存储桶的 UTC 结束时间 |
| *instanceData* |实例 （采用新格式） 的详细信息的键 / 值对：<br>  *resourceUri*： 完全限定的资源 ID，包括资源组和实例名称 <br>  *位置*： 在其中运行此服务的区域 <br>  *标记*： 用户指定的资源标记 <br>  *additionalInfo*： 更详细说明了有关已使用，例如，资源 OS 版本或图像类型 |
| *quantity* |这段时间内发生的资源消耗的量 |
| *meterId* |已耗用的资源的唯一 ID (也称为*ResourceID*) |


## <a name="next-steps"></a>后续步骤
[提供程序资源使用情况 API](azure-stack-provider-resource-api.md)

[有关使用情况的常见问题解答](azure-stack-usage-related-faq.md)


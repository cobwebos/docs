---
title: 使用 REST API 检索指标
titleSuffix: Azure Load Balancer
description: 本文开始使用 Azure REST Api 来收集 Azure 负载均衡器的运行状况和使用情况指标。
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 760ec8a945ab88b63dde2de75f5354818facf4f2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225256"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>使用 REST API 获取负载均衡器使用情况指标

使用[Azure REST API](/rest/api/azure/)收集[标准负载均衡器](/azure/load-balancer/load-balancer-standard-overview)为时间间隔处理的字节数。

[Azure Monitor REST 参考](/rest/api/monitor)中提供了完整的参考文档和 REST API 的其他示例。 

## <a name="build-the-request"></a>生成请求

使用以下 GET 请求从标准负载均衡器收集 [ByteCount 指标](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics)。 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>请求标头

以下标头是必需的： 

|请求标头|说明|  
|--------------------|-----------------|  
|Content-Type：|必需。 设置为 `application/json`。|  
|Authorization：|必需。 设置为有效的 `Bearer` [访问令牌](/rest/api/azure/#authorization-code-grant-interactive-clients)。 |  

### <a name="uri-parameters"></a>URI 参数

| 名称 | 说明 |
| :--- | :---------- |
| subscriptionId | 用于标识 Azure 订阅的订阅 ID。 如果拥有多个订阅，请参阅[使用多个订阅](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。 |
| resourceGroupName | 包含该资源的资源组名称。 可以从 Azure 资源管理器 API、CLI 或门户获取此值。 |
| loadBalancerName | Azure 负载均衡器的名称。 |
| 指标名称 | 以逗号分隔的有效[负载均衡器指标](/azure/load-balancer/load-balancer-standard-diagnostics)列表。 |
| api-version | 要用于请求的 API 版本。<br /><br /> 本文档涵盖 API 版本 `2018-01-01`，包含于上述 URL 中。  |
| timespan | 查询的时间跨度。 这是一个字符串，格式如下 `startDateTime_ISO/endDateTime_ISO`。 此可选参数设置为在示例中返回一天的数据。 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>请求正文

此操作无需任何请求正文。

## <a name="handle-the-response"></a>处理响应

成功返回指标值列表时，返回状态代码 200。 [参考文档](/rest/api/monitor/metrics/list#errorresponse)中提供了错误代码的完整列表。

## <a name="example-response"></a>示例响应 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```

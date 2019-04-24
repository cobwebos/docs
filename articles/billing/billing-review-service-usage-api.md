---
title: 使用 REST API 查看 Azure 服务资源使用情况 | Microsoft Docs
description: 了解如何使用 Azure REST API 查看 Azure 服务资源的使用情况。
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: erikre
ms.openlocfilehash: d3db4166810da981ff0117536d8550a6b2203924
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370979"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>使用 REST API 查看 Azure 资源使用情况

Azure 成本管理 API 可帮助你查看和管理 Azure 资源的消耗量。

本文介绍如何创建每日报告（可生成包含每小时使用情况信息的逗号分隔值文档），然后介绍如何使用筛选器来自定义报告，以便能够查询 Azure 资源组中虚拟机、数据库和已标记的资源的使用情况。

>[!NOTE]
> 成本管理 API 目前以个人预览版提供。

## <a name="create-a-basic-cost-management-report"></a>创建基本的成本管理报告

使用成本管理 API 中的 `reports` 操作可以定义如何生成成本报告，以及要将报告发布到何处。

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

`{subscriptionGuid}` 参数是必需的，应该包含可使用 API 令牌中提供的凭据进行读取的订阅 ID。 `{reportName}`

以下标头是必需的： 

|请求标头|描述|  
|--------------------|-----------------|  
|Content-Type：| 必需。 设置为 `application/json`。 |  
|Authorization：| 必需。 设置为有效的 `Bearer` 令牌。 |

在 HTTP 请求正文中配置报告的参数。 在以下示例中，报告设置为每日活动时生成，它是一个写入 Azure 存储 Blob 容器的 CSV 文件，包含资源组 `westus` 中所有资源的每小时成本信息。

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

The

## <a name="filtering-reports"></a>筛选报告

创建报告时，请求正文的 `filter` 和 `dimensions` 节可让你专注于特定资源类型的成本。 上面的请求正文演示如何按区域中的所有资源进行筛选。 

### <a name="get-all-compute-usage"></a>获取所有计算使用情况

使用 `ResourceType` 维度可以报告所有区域的订阅中的 Azure 虚拟机成本。

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines", 
                "Microsoft.Compute/virtualMachines"
        ] 
    }
}
```

### <a name="get-all-database-usage"></a>获取所有数据库使用情况

使用 `ResourceType` 维度可以报告所有区域的订阅中的 Azure SQL 数据库成本。

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ] 
    }
}
```

### <a name="report-on-specific-instances"></a>根据特定的实例进行报告

使用 `Resource` 维度可以报告特定资源的成本。

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>更改时间范围

将 `timeframe` 定义设置为 `Custom` 可以设置星期日期和月份日期内置选项中未提供的时间范围。

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>后续步骤
- [Azure REST API 入门](https://docs.microsoft.com/rest/api/azure/)   

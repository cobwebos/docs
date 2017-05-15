---
title: "Azure 计费企业 API - 使用情况详细信息 | Microsoft Docs"
description: "了解 Azure 帐单用量和 RateCard API，洞察 Azure 资源消耗量和趋势。"
services: 
documentationcenter: 
author: aedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: d6fe7a020100e3372c1fa0e244838d4c40839dbf
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---usage-details-preview"></a>适用于企业客户的报告 API - 使用情况详细信息（预览版）

使用情况详细信息 API 提供已耗用量和注册估计费用的日常明细。 结果还包括有关实例、计量和部门信息。 可以按照计费周期或指定的开始日期和结束日期查询 API。 
## <a name="consumption-apis"></a>消耗量 API


##<a name="request"></a>请求 
[此处](billing-enterprise-api.md)指定了需要添加的通用标头属性。 如果未指定计费周期，则返回当前计费周期的数据。 可以使用 yyyy-MM-dd 格式的开始日期和结束日期参数指定自定义时间范围。 支持的最大时间范围为 36 个月。  

|方法 | 请求 URI|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

## <a name="response"></a>响应

> 由于数据量可能很大，因此会将结果集分页。 如果存在 nextLink 属性，该属性将指定下一页数据的链接。 如果链接为空，则表示这是最后一页。 
<br/>

    {
        "id": "string",
        "data": [
            {                        
            "accountId": 0,
            "productId": 0,
            "resourceLocationId": 0,
            "consumedServiceId": 0,
            "departmentId": 0,
            "accountOwnerEmail": "string",
            "accountName": "string",
            "serviceAdministratorId": "string",
            "subscriptionId": 0,
            "subscriptionGuid": "string",
            "subscriptionName": "string",
            "date": "2017-04-27T23:01:43.799Z",
            "product": "string",
            "meterId": "string",
            "meterCategory": "string",
            "meterSubCategory": "string",
            "meterRegion": "string",
            "meterName": "string",
            "consumedQuantity": 0,
            "resourceRate": 0,
            "extendedCost": 0,
            "resourceLocation": "string",
            "consumedService": "string",
            "instanceId": "string",
            "serviceInfo1": "string",
            "serviceInfo2": "string",
            "additionalInfo": "string",
            "tags": "string",
            "storeServiceIdentifier": "string",
            "departmentName": "string",
            "costCenter": "string",
            "unitOfMeasure": "string",
            "resourceGroup": "string"
            }
        ]
        "nextLink": "string"
    }

<br/>

响应属性定义

|属性名称| 类型| 说明
|-|-|-|
|id| 字符串| API 调用的唯一 ID。 |
|数据| JSON 数组| 每个 instance\meter 的日常使用情况详细信息数组|
|nextLink| 字符串| 存在多页数据时，nextLink 将指向返回下一页数据的 URL |

## <a name="see-also"></a>另请参阅
* [计费周期 API](billing-enterprise-api-billing-periods.md)

* [余额和摘要 API](billing-enterprise-api-balance-summary.md)

* [应用商店费用 API](billing-enterprise-api-marketplace-storecharge.md) 

* [价目表 API](billing-enterprise-api-pricesheet.md)

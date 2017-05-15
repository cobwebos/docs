---
title: "Azure 计费企业 API - 应用商店费用 | Microsoft Docs"
description: "了解使企业 Azure 客户能够以编程方式提取消耗数据的报告 API。"
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
ms.openlocfilehash: 9f084dce3f01466aaa0e4c32d339c925d9faccd3
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---marketplace-charges-preview"></a>适用于企业客户的报告 API - 应用商店费用（预览版）

应用商店费用 API 会返回基于使用情况的应用商店费用明细（不包括一次性费用），且按指定计费周期或开始和结束日期排列。

##<a name="request"></a>请求 
[此处](billing-enterprise-api.md)指定了需要添加的通用标头属性。 如果未指定计费周期，则返回当前计费周期的数据。 可以使用 yyyy-MM-dd 格式的开始日期和结束日期参数指定自定义时间范围，支持的最大时间范围为 36 个月。  

|方法 | 请求 URI|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/marketplacecharges|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/marketplacecharges|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/marketplacechargesbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

## <a name="response"></a>响应
 
    
        [
            {
                "id": "id",
                "subscriptionGuid": "00000000-0000-0000-0000-000000000000",
                "subscriptionName": "subName",
                "meterId": "2core",
                "usageStartDate": "2015-09-17T00:00:00Z",
                "usageEndDate": "2015-09-17T23:59:59Z",
                "offerName": "Virtual LoadMaster™ (VLM) for Azure",
                "resourceGroup": "Res group",
                "instanceId": "id",
                "additionalInfo": "{\"ImageType\":null,\"ServiceType\":\"Medium\"}",
                "tags": "",
                "orderNumber": "order",
                "unitOfMeasure": "",
                "costCenter": "100",
                "accountId": 100,
                "accountName": "Account Name",
                "accountOwnerId": "account@live.com",
                "departmentId": 101,
                "departmentName": "Department 1",
                "publisherName": "Publisher 1",
                "planName": "Plan name",
                "consumedQuantity": 1.15,
                "resourceRate": 0.1,
                "extendedCost": 1.11
            },
            ...
        ]
    

响应属性定义

|属性名称| 类型| 说明
|-|-|-|
|id|字符串|应用商店费用项的唯一 ID|
|subscriptionGuid|Guid|订阅 Guid|
|subscriptionName|字符串|订阅名称|
|meterId|字符串|已发出计量的 ID|
|usageStartDate|DateTime|使用情况记录的开始时间|
|usageEndDate|DateTime|使用情况记录的结束时间|
|offerName|字符串|产品/服务名称|
|resourceGroup|字符串|资源组|
|instanceId|字符串|实例 ID|
|additionalInfo|字符串|其他信息 JSON 字符串|
|标记|字符串|标记 JSON 字符串|
|orderNumber|字符串|订单号|
|unitOfMeasure|字符串|计量的度量单位|
|costCenter|字符串|成本中心|
|accountId|int|帐户 ID|
|accountName|字符串 |帐户名|
|accountOwnerId|字符串|帐户所有者 ID|
|departmentId|int|部门 ID|
|departmentName|字符串|部门名称|
|publisherName|字符串|发布者名称|
|planName|字符串|计划名称|
|consumedQuantity|decimal|此时间段的已耗用数量|
|resourceRate|decimal|计量的单位价格|
|extendedCost|decimal|基于已耗用数量和扩展成本的估计费用|
<br/>
## <a name="see-also"></a>另请参阅
* [计费周期 API](billing-enterprise-api-billing-periods.md)

* [使用情况详细信息 API](billing-enterprise-api-usage-detail.md) 

* [余额和摘要 API](billing-enterprise-api-balance-summary.md)

* [价目表 API](billing-enterprise-api-pricesheet.md)

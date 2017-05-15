---
title: "Azure 计费企业 API - 计费周期 | Microsoft Docs"
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
ms.openlocfilehash: ab8d7fefb64b1358bb1b9667d280cb53fc2f636c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---billing-periods-preview"></a>适用于企业客户的报告 API - 计费周期（预览版）

计费周期 API 会以倒序顺序为指定的注册返回具有消耗数据的计费周期列表。 每个周期都有一个属性指向以下四个数据集的 API 路由：BalanceSummary、UsageDetails、Marketplace Charge 和 PriceSheet。 如果周期没有数据，则对应的属性为 null。 


##<a name="request"></a>请求 
[此处](billing-enterprise-api.md)指定了需要添加的通用标头属性。 

|方法 | 请求 URI|
|-|-|
|GET| https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingperiods|


## <a name="response"></a>响应
 
    
    
        [
            {
                  "billingPeriodId": "201704",
                  "billingStart": "2017-04-01T00:00:00Z",
                  "billingEnd": "2017-04-30T11:59:59Z",
                "balanceSummary": "/v1/enrollments/100/billingperiods/201704/balancesummary",
                  "usageDetails": "/v1/enrollments/100/billingperiods/201704/usagedetails",
                  "marketplaceCharges": "/v1/enrollments/100/billingperiods/201704/marketplacecharges",
                  "priceSheet": "/v1/enrollments/100/billingperiods/201704/pricesheet"
            },            
            ....
        ]
    

响应属性定义

|属性名称| 类型| 说明
|-|-|-|
|billingPeriodId| 字符串| 表示特定计费周期的唯一 ID|
|billingStart| datetime| 表示周期开始日期的 ISO 8601 字符串|
|billingEnd| datetime| 表示周期结束日期的 ISO 8601 字符串|
|balanceSummary| 字符串| 路由到此周期的“余额摘要”数据的 URL 路径|
|usageDetails| 字符串| 路由到此周期的“使用情况详细信息”数据的 URL 路径|
|marketplaceCharges| 字符串| 路由到此周期的“应用商店费用”数据的 URL 路径|
|priceSheet| 字符串| 路由到此周期的“价目表”数据的 URL 路径|

<br/>
## <a name="see-also"></a>另请参阅
* [余额和摘要 API](billing-enterprise-api-balance-summary.md)

* [使用情况详细信息 API](billing-enterprise-api-usage-detail.md) 

* [应用商店费用 API](billing-enterprise-api-marketplace-storecharge.md) 

* [价目表 API](billing-enterprise-api-pricesheet.md)

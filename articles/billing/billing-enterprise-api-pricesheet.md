---
title: "Azure 计费企业 API - PriceSheet | Microsoft Docs"
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
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 2e7d6e883abe4cee13bc5f684baf2a1ea9c6c397
ms.contentlocale: zh-cn
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---price-sheet"></a>适用于企业客户的报告 API - 价目表

价目表 API 为给定注册和计费周期的每个计量提供适用的费率。

##<a name="request"></a>请求
[此处](billing-enterprise-api.md)指定了需要添加的通用标头属性。 如果未指定计费周期，则返回当前计费周期的数据。

|方法 | 请求 URI|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet|

> [!Note]
> 若要使用预览版 API，请在上述 URL 中将 v2 替换为 v1。
>

## <a name="response"></a>响应

    
        [
            {
                "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
                "billingPeriodId": "201704",
                "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
                "meterName": "A1 VM",
                "unitOfMeasure": "100 Hours",
                "includedQuantity": 0,
                "partNumber": "N7H-00015",
                "unitPrice": 0.00,
                "currencyCode": "USD"
            },
            {
                "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
                "billingPeriodId": "201404",
                "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
                "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
                "unitOfMeasure": "100 GB",
                "includedQuantity": 0,
                "partNumber": "N9H-00402",
                "unitPrice": 0.00,
                "currencyCode": "USD"
            },
            ...
        ]
    

> [!Note]
>如果使用的是 API 预览版，则 meterId 字段不可用。
>

响应属性定义

|属性名称| 类型| 说明
|-|-|-|
|id| 字符串| 表示特定 PriceSheet 项的唯一 ID（按计费周期计量）|
|billingPeriodId| 字符串| 表示特定计费周期的唯一 ID|
|meterId| 字符串| 测定仪的标识符。 其可以映射到使用情况 meterId。|
|meterName| 字符串| 计量名称|
|unitOfMeasure| 字符串| 用于测定服务的计量单位|
|includedQuantity| decimal| 包含的数量 |
|partNumber| 字符串| 与计量关联的部件号|
|unitPrice| decimal| 计量的单位价格|
|currencyCode| 字符串| UnitPrice 的货币代码|
<br/>
## <a name="see-also"></a>另请参阅

* [计费周期 API](billing-enterprise-api-billing-periods.md)

* [使用情况详细信息 API](billing-enterprise-api-usage-detail.md)

* [余额和摘要 API](billing-enterprise-api-balance-summary.md)

* [应用商店费用 API](billing-enterprise-api-marketplace-storecharge.md)


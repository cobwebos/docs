---
title: "Azure 计费企业 API - 余额和摘要 | Microsoft Docs"
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
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: f6b149f0e656d2263705048aa5b644f5bb4a5712
ms.contentlocale: zh-cn
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---balance-and-summary"></a>适用于企业客户的报告 API - 余额和摘要

余额和摘要 API 提供了关于余额、新购买、Azure Marketplace 服务费用、调整和超额费用信息的每月摘要。


##<a name="request"></a>请求 
[此处](billing-enterprise-api.md)指定了需要添加的通用标头属性。 如果未指定计费周期，则返回当前计费周期的数据。

|方法 | 请求 URI|
|-|-|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/balancesummary|
|GET| https://consumption.azure.com/v2/enrollments/ {enrollmentNumber} / balancesummary|

> [!Note]
> 若要使用预览版 API，请在上述 URL 中将 v2 替换为 v1。
>

## <a name="response"></a>响应

        {
            "id": "enrollments/100/billingperiods/201507/balancesummaries",
            "billingPeriodId": 201507,
            "currencyCode": "USD",
            "beginningBalance": 0,
            "endingBalance": 1.1,
            "newPurchases": 1,
            "adjustments": 1.1,
            "utilized": 1.1,
            "serviceOverage": 1,
            "chargesBilledSeparately": 1,
            "totalOverage": 1,
            "totalUsage": 1.1,
            "azureMarketplaceServiceCharges": 1,
            "newPurchasesDetails": [
                {
                "name": "",
                "value": 1
                }
            ],
            "adjustmentDetails": [
                {
                "name": "Promo Credit",
                "value": 1.1
                },
                {
                "name": "SIE Credit",
                "value": 1.0
                }
            ]
        }


响应属性定义

|属性名称| 类型| 说明
|-|-|-|
|id|字符串|特定计费周期和注册的唯一 ID|
|billingPeriodId|字符串 |计费周期 ID|
|currencyCode|字符串 |货币代码|
|beginningBalance|decimal| 计费周期的起始余额|
|endingBalance|decimal| 计费周期的期末余额（对于开放期，此余额将每日更新）|
|newPurchases|decimal| 新购买总量|
|调整|decimal| 调整总金额|
|已用量|decimal| 承诺使用总量|
|serviceOverage|decimal| Azure 服务超额|
|chargesBilledSeparately|decimal| 单独计费收费|
|totalOverage|decimal| serviceOverage + chargesBilledSeparately|
|totalUsage|decimal| Azure 服务承诺 + 总超额|
|azureMarketplaceServiceCharges|decimal| Azure 应用商店的总费用|
|newPurchasesDetails|名称值对的 JSON 字符串数组|新购买列表|
|adjustmentDetails|名称值对的 JSON 字符串数组|调整列表（促销贷方、SIE 贷方等） |


<br/>
## <a name="see-also"></a>另请参阅

* [计费周期 API](billing-enterprise-api-billing-periods.md)

* [使用情况详细信息 API](billing-enterprise-api-usage-detail.md) 

* [应用商店费用 API](billing-enterprise-api-marketplace-storecharge.md) 

* [价目表 API](billing-enterprise-api-pricesheet.md)

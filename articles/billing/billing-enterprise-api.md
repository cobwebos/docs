---
title: Azure 计费企业 API | Microsoft Docs
description: 了解使企业 Azure 客户能够以编程方式提取消耗数据的报告 API。
services: ''
documentationcenter: ''
author: anandedwin
manager: aedwin
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.openlocfilehash: 7ae04c7c1fdddecf40583d14f7b08dcf41941c6f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>面向企业客户的报告 API 概述
报告 API 使企业 Azure 客户能够以编程方式将消耗数据和计费数据提取到首选的数据分析工具。 

## <a name="enabling-data-access-to-the-api"></a>实现对 API 的数据访问
* **生成或检索 API 密钥** - 登录到 Enterprise Portal，并导航到“报告”>“下载使用情况”>“API 访问密钥”，生成或检索 API 密钥。
* 传递 API 密钥 - 需要为每个调用传递 API 密钥，以便进行身份验证和授权。 以下属性需要是 HTTP 标头

|请求标头密钥 | 值|
|-|-|
|授权| 用以下格式指定该值：bearer {API_KEY} <br/> 示例：bearer eyr....09|

## <a name="consumption-apis"></a>消耗量 API
可在[此处](https://consumption.azure.com/swagger/ui/index)找到为下面所述的 API 提供的 Swagger 终结点，该终结点可以使用 [AutoRest](https://github.com/Azure/AutoRest) 或 [Swagger CodeGen](http://swagger.io/swagger-codegen/) 进行简单的 API 自检并生成客户端 SDK。 2014 年 5 月 1 日开始的数据就是通过此 API 提供的。 

* **余额和摘要** - [余额和摘要 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) 提供关于余额、新购买、Azure Marketplace 服务费用、调整和超额费用信息的每月摘要。

* 使用情况详细信息 - [使用情况详细信息 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) 提供已耗用量和注册估计费用的日常明细。 结果还包括有关实例、计量和部门信息。 可以按照计费周期或指定的开始日期和结束日期查询 API。 

* **Marketplace 应用商店费用** - [Marketplace 应用商店费用 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) 会返回基于使用情况的 Marketplace 费用明细（不包括一次性费用），且按指定计费周期的天或开始和结束日期排列。

* 价目表 - [价目表 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) 为给定注册和计费周期的每个计量提供适用的费率。 

## <a name="data-freshness"></a>数据刷新
在上述所有 API 的响应中将返回 Etag。 Etag 中的更改指示数据已刷新。  在使用相同参数对同一 API 的后续调用中，将使用 http 请求标头中的键“If-None-Match”传递捕获的 Etag。 如果没有进一步刷新数据，则响应状态代码将为“NotModified”且不会返回任何数据。 只要存在 etag 更改，API 就会返回所需时段内的完整数据集。

## <a name="helper-apis"></a>帮助程序 API
 列出计费周期 - [计费周期 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) 会以倒序顺序为指定的注册返回具有消耗数据的计费周期列表。 每个周期都有一个属性指向以下 4 个数据集的 API 路由：BalanceSummary、UsageDetails、Marketplace Charge 和 PriceSheet。


## <a name="api-response-codes"></a>API 响应代码  
|响应状态代码|消息|说明|
|-|-|-|
|200| OK|无错误|
|401| 未授权| API 密钥找不到、无效、已过期等。|
|404| 不可用| 找不到报表终结点|
|400| 错误的请求| 参数无效 – 日期范围、EA 号等。|
|500| 服务器错误| 处理请求时出现异常错误| 










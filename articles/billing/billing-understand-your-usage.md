---
title: 了解详细的使用量和费用 |Microsoft Docs
description: 了解如何阅读并理解详细使用情况和费用
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 2eb9f8e19be2a7b6220bc34bf4ce0c72c4ac0b4f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275066"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>了解 Azure 的使用情况和费用文件中的条款

详细的使用情况和费用文件包含协商的率、 购买 （例如，预订，收取的 Marketplace） 和退款基于指定时段内的每日已评分的使用情况。
费用不包括信用额度、 税款，或其他费用或折扣。
下表介绍，为每个帐户类型提供了哪些费用。

帐户类型 | Azure 使用情况 | Marketplace 使用情况 | 购买 | 退款
--- | --- | --- | --- | ---
企业协议 (EA) | 是 | 是 | 是 | 否
Microsoft 客户协议 (MCA) | 是 | 是 | 是 | 是
即用即付 (PAYG) | 是 | 否 | 否 | 否

若要了解有关 Marketplace 订单 （也称为外部服务） 的详细信息，请参阅[了解 Azure 外部服务费用](billing-understand-your-azure-marketplace-charges.md)。

请参阅[如何获取 Azure 帐单发票和每日使用情况数据](billing-download-azure-invoice-daily-usage-date.md)有关下载说明。
使用情况和费用文件现已推出的以逗号分隔值 (.csv) 文件格式，可以在电子表格应用程序中打开。

## <a name="list-of-terms-and-descriptions"></a>条款和说明的列表

下表描述了使用 Azure 使用情况和费用文件的最新版本中的重要术语。
列表介绍了即用即付 (PAYG)、 企业协议 (EA) 和 Microsoft 客户协议 (MCA) 帐户。

术语 | 帐户类型 | 描述
--- | --- | ---
AccountName | EA | 注册帐户的显示名称。
AccountOwnerId | EA | 注册帐户的唯一标识符。
AdditionalInfo | 全部 | 服务特定的元数据。 例如，虚拟机的映像类型。
BillingAccountId | EA, MCA | 计费帐户的根的唯一标识符。
BillingAccountName | EA, MCA | 计费帐户的名称。
BillingCurrency | EA, MCA | 与计费帐户关联的货币。
BillingPeriod | EA | 计费周期的费用。
BillingPeriodEndDate | EA, MCA | 在计费周期结束日期。
BillingPeriodStartDate | EA, MCA | 在计费周期开始日期。
BillingProfileId | EA, MCA | EA 注册或 MCA 计费配置文件的唯一标识符。
BillingProfileName | EA, MCA | EA 注册或 MCA 计费配置文件的名称。
ChargeType | EA, MCA | 指示该费用是否表示使用情况 (**使用情况**)，购买 (**购买**)，或退款 (**退款**)。
ConsumedQuantity | PAYG | 请参阅数量。
ConsumedService | 全部 | 与关联的服务费用的名称。
成本 | EA | 请参阅 CostInBillingCurrency。
CostCenter | EA, MCA | 定义用于跟踪成本 （仅在打开的 MCA 帐户的计费周期中可用） 的订阅成本中心。
CostInBillingCurrency | MCA | 计费货币信用额度或税前费用的成本。
CostInPricingCurrency | MCA | 定价的货币信用额度或税前费用的成本。
Currency | PAYG | 请参阅 BillingCurrency。
Date | EA, MCA | 电量使用情况或购买日期。
ExchangeRateDate | MCA | 已建立汇率的日期。
ExchangeRatePricingToBilling | MCA | 用于将定价货币成本转换为计费货币的汇率。
频率 | EA, MCA | 指示是否应费用重复。 费用是可以执行一次 (**OneTime**)，请重复按每月或每年 (**重复执行**)，或根据使用情况 (**UsageBased**)。
IncludedQuantity | PAYG | 在当前计费周期中免费附送的计量的量。
InstanceId | PAGY | 资源 Id，请参阅。
InvoiceId | EA, MCA | 发票 PDF 上列出的唯一的文档 ID。
InvoiceSection | MCA | 请参阅 InvoiceSectionName。
InvoiceSectionId | EA, MCA | EA 部门或 MCA 发票部分的唯一标识符。
InvoiceSectionName | EA, MCA | EA 部门或 MCA 发票部分的名称。
IsAzureCreditEligible | EA, MCA | 指示是否可为使用 Azure 信用额度支付费用 (值：True、 False)。
IsEstimated | 全部 | 指示是否关闭/完成计费周期。 使用情况数据可以更改整个计费周期之前生成发票。 这些记录将被标记为"估计"。
Location | EA, MCA | 该资源正在其中运行的数据中心位置。
MeterCategory | 全部 | 测定仪分类类别的名称。 例如，*云服务*并*网络*。
计量 ID | 全部 | 计量的唯一标识符。
MeterName | 全部 | 计量的名称。
MeterRegion | 全部 | 基于位置的数据中心位置定价的服务的名称。 请参阅位置。
MeterSubCategory | 全部 | 计量子分类类别的名称。
OfferId | EA, MCA | 购买的产品/服务的名称。
PartNumber | EA | 用于获取特定计量定价的标识符。
planName | EA | Marketplace 计划名称。
PreviousInvoiceId | MCA | 对原始发票如果此行项是退款的引用。
PricingCurrency | MCA | 评级基于协商价格时使用的货币。
产品 | MCA | 请参阅产品名称。
ProductId | EA, MCA | 该产品的唯一标识符。
ProductName | EA | 产品的名称。
ProductOrderId | EA, MCA | 产品订单的唯一标识符。
ProductOrderName | EA, MCA | 产品订单的唯一名称。
PublisherName | EA, MCA | 对 Marketplace 服务的发布服务器。
PublisherType | EA, MCA | 发布服务器的类型 (值： firstParty，thirdPartyReseller，thirdPartyAgency)。
数量 | EA, MCA | 购买或使用的单位数。
费率 | PAYG | 请参阅单价。
预订 ID | EA, MCA | 购买的预订实例的唯一标识符。
ReservationName | EA, MCA | 购买的预订实例的名称。
ResourceGroupId | EA, MCA | 唯一标识符[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)资源处于。
ResourceGroupName | EA, MCA | 名称[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)资源处于。
ResourceId | EA, MCA | 唯一标识符[Azure 资源管理器](https://docs.microsoft.com/rest/api/resources/resources)资源。
ResourceLocation | EA, MCA | 该资源正在其中运行的数据中心位置。 请参阅位置。
ResourceName | EA | 资源的名称。
ResourceType | MCA | 资源实例的类型。
ServiceFamily | EA, MCA | 服务所属的服务系列。
ServiceInfo1 | 全部 | 服务特定的元数据。
ServiceInfo2 | 全部 | 使用可选的服务特定元数据的旧字段。
ServicePeriodEndDate | MCA | 分级周期的结束日期的定义和锁定的已使用或购买服务的定价。
ServicePeriodStartDate | MCA | 分级周期的开始日期的定义和锁定的已使用或购买服务的定价。
SubscriptionId | 全部 | 订阅的的唯一标识符。
SubscriptionName | 全部 | 订阅的名称。
标记 | 全部 | 分配给资源标记。 不包括资源组标记。 可用于分组或分配内部退款的费用。 有关详细信息，请参阅[使用标记来组织 Azure 资源](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)。
单位 | PAYG | 请参阅 UnitOfMeasure。
UnitOfMeasure | 全部 | 对服务计费的度量单位。 例如，计算每小时对服务进行计费。
UnitPrice | EA | 每个单位的费用价格。
UsageDate | PAYG | 请参阅日期。

请注意某些字段的大小写和帐户类型之间的间距可能有所不同。
较旧版本的即用即付的使用情况文件有各单独章节的语句和每日使用情况。

## <a name="ensure-that-your-charges-are-correct"></a>确保你的费用是正确的

若要了解有关详细的使用量和费用的详细信息，请阅读有关如何了解你[即用即付](./billing-understand-your-bill.md)或[Microsoft 客户协议](billing-mca-understand-your-bill.md)发票。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [查看和下载 Microsoft Azure 发票](billing-download-azure-invoice.md)
- [查看和下载你的 Microsoft Azure 使用情况和费用](billing-download-azure-daily-usage.md)

---
title: 了解详细的使用情况和费用 |Microsoft Docs
description: 了解如何阅读并了解详细的使用情况和费用
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
ms.openlocfilehash: 66b54c027cde6341b23aef2c10b43fa21bf357da
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383464"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>了解 Azure 使用情况和费用文件中的条款

详细的使用情况和费用文件包含基于协商费率、购买 (例如, 预订、Marketplace 费用) 和指定期间的退款的每日分级使用情况。
费用不包括信用、税款或其他费用或折扣。
下表介绍了每种帐户类型的费用。

帐户类型 | Azure 使用情况 | Marketplace 使用情况 | 订购 | 退款
--- | --- | --- | --- | ---
企业协议 (EA) | 是 | 是 | 是 | 否
Microsoft 客户协议 (MCA) | 是 | 是 | 是 | 是
即用即付 (PAYG) | 是 | 否 | 否 | 否

若要了解有关 Marketplace 订单 (也称为外部服务) 的详细信息, 请参阅[了解 Azure 外部服务费用](billing-understand-your-azure-marketplace-charges.md)。

有关下载说明, 请参阅[如何获取 Azure 帐单发票和每日使用数据](billing-download-azure-invoice-daily-usage-date.md)。
你可以在 Microsoft Excel 或其他电子表格应用程序中打开你的使用情况和费用 CSV 文件。

## <a name="list-of-terms-and-descriptions"></a>术语和说明列表

下表描述了 Azure 使用情况和费用文件的最新版本中使用的重要术语。
此列表涵盖即用即付 (PAYG)、企业协议 (EA) 和 Microsoft 客户协议 (MCA) 帐户。

术语 | 帐户类型 | 描述
--- | --- | ---
帐户名 | EA、PAYG | 显示 EA 注册帐户或 PAYG 计费帐户的名称。
AccountOwnerId | EA、PAYG | EA 注册帐户或 PAYG 计费帐户的唯一标识符。
AdditionalInfo | 全部 | 服务特定的元数据。 例如，虚拟机的映像类型。
BillingAccountId | 全部 | 根计费帐户的唯一标识符。
BillingAccountName | 全部 | 计费帐户的名称。
BillingCurrency | 全部 | 与计费帐户关联的货币。
BillingPeriod | EA、PAYG | 计费的计费周期。
BillingPeriodEndDate | 全部 | 计费周期的结束日期。
BillingPeriodStartDate | 全部 | 计费周期的开始日期。
BillingProfileId | 全部 | EA 注册、PAYG 订阅、MCA 计费配置文件或 AWS 合并帐户的唯一标识符。
BillingProfileName | 全部 | EA 注册、PAYG 订阅、MCA 计费配置文件或 AWS 合并帐户的名称。
ChargeType | 全部 | 指示费用是表示使用情况 (**使用情况**)、采购 (**购买**) 还是退款 (**退款**)。
ConsumedService | 全部 | 与费用关联的服务的名称。
成本中心 | EA, MCA | 为订阅定义的成本中心 (仅适用于 MCA 帐户的开放计费周期)。
开销 | EA、PAYG | 请参阅 CostInBillingCurrency。
CostInBillingCurrency | MCA | 在信用额度或税前计费的费用。
CostInPricingCurrency | MCA | 在信用额度和税前定价中的费用成本。
货币 | EA、PAYG | 请参阅 BillingCurrency。
Date | 全部 | 费用的使用量或购买日期。
EffectivePrice | 全部 | 此期间的混合单价。 混合价格平均超出了单位价格的任何波动, 如渐变分层, 这会随着时间的推移而增加价格。
ExchangeRateDate | MCA | 汇率的创建日期。
ExchangeRatePricingToBilling | MCA | 用于将定价货币中的成本转换为计费货币的汇率。
频率 | 全部 | 指示是否应重复收取费用。 费用可能发生一次 (**一次性**)、每月或每年重复一次 (**定期**) 或基于使用情况 (**UsageBased**)。
IncludedQuantity | PAYG | 当前计费期间内免费提供的计量量。
InstanceId | PAGY | 请参阅 ResourceId。
InvoiceId | PAYG, MCA | 发票 PDF 上列出的唯一文档 ID。
InvoiceSection | MCA | 请参阅 InvoiceSectionName。
InvoiceSectionId | EA, MCA | EA 部门或 MCA 发票部分的唯一标识符。
InvoiceSectionName | EA, MCA | EA 部门或 MCA 发票部分的名称。
IsAzureCreditEligible | 全部 | 指示是否有资格使用 Azure 额度支付费用 (值:True、False)。
Location | MCA | 运行资源的数据中心位置。
计量类别 | 全部 | 计量的分类类别的名称。 例如,*云服务*和*网络*。
计量 ID | 全部 | 计量器的唯一标识符。
计量名称 | 全部 | 计量器的名称。
MeterRegion | 全部 | 基于位置定价的服务的数据中心位置的名称。 请参阅位置。
MeterSubCategory | 全部 | 计量 subclassification 类别的名称。
OfferId | 全部 | 购买的产品/服务的名称。
PartNumber | EA、PAYG | 用于获取特定计量定价的标识符。
PlanName | EA、PAYG | Marketplace 计划名称。
PreviousInvoiceId | MCA | 如果此行项是退款, 则引用原始发票。
PricingCurrency | MCA | 基于协商价格进行评级时使用的货币。
产品 | 全部 | 产品的名称。
产品 ID | MCA | 产品的唯一标识符。
ProductOrderId | 全部 | 产品订单的唯一标识符。
ProductOrderName | 全部 | 产品顺序的唯一名称。
PublisherName | 全部 | 适用于 Marketplace 服务的发布者。
PublisherType | 全部 | 发布服务器的类型 (值:**Azure**、 **AWS**、 **Marketplace**)。
数量 | 全部 | 购买或消耗的单位数。
预订 ID | EA, MCA | 采购预订实例的唯一标识符。
ReservationName | EA, MCA | 已购买的预订实例的名称。
资源组 | 全部 | 资源所在的[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的名称。
resourceId | 全部 | [Azure 资源管理器](https://docs.microsoft.com/rest/api/resources/resources)资源的唯一标识符。
ResourceLocation | 全部 | 运行资源的数据中心位置。 请参阅位置。
ResourceName | EA、PAYG | 资源的名称。
ResourceType | MCA | 资源实例的类型。
ServiceFamily | MCA | 服务所属的服务系列。
ServiceInfo1 | 全部 | 服务特定的元数据。
ServiceInfo2 | 全部 | 具有可选服务特定元数据的旧字段。
ServicePeriodEndDate | MCA | 已为使用或购买的服务定义和锁定定价的评估期的结束日期。
ServicePeriodStartDate | MCA | 为已用或已购买的服务定义和锁定定价的评估期的开始日期。
SubscriptionId | 全部 | Azure 订阅的唯一标识符。
订阅名称 | 全部 | Azure 订阅的名称。
Tags | 全部 | 分配给资源的标记。 不包含资源组标记。 可用于对内部费用分摊进行分组或分配成本。 有关详细信息，请参阅[使用标记来组织 Azure 资源](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)。
UnitOfMeasure | 全部 | 服务的计费度量单位。 例如, 计算服务按小时计费。
单价 | EA、PAYG | 收费单位的价格。

请注意, 某些字段的大小写与帐户类型之间的间距可能不同。
旧版本的即用即付使用量文件在语句和每日使用中都有单独的部分。

### <a name="list-of-terms-from-older-apis"></a>旧版 Api 中的术语列表
下表将旧版 Api 中使用的术语映射到新术语。 请参阅上表了解相关说明。

旧术语 | 新建术语
--- | ---
已使用数量 | 数量
IncludedQuantity | 不可用
InstanceId | resourceId
资费 | EffectivePrice
单位 | UnitOfMeasure
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>确保费用正确

若要详细了解详细使用情况和费用, 请阅读如何了解即[用即付](./billing-understand-your-bill.md)或[Microsoft 客户协议](billing-mca-understand-your-bill.md)发票。

## <a name="need-help-contact-us"></a>需要帮助? 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [查看并下载 Microsoft Azure 发票](billing-download-azure-invoice.md)
- [查看并下载你的 Microsoft Azure 使用情况和费用](billing-download-azure-daily-usage.md)

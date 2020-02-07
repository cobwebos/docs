---
title: 了解详细的使用情况和费用 | Microsoft Docs
description: 介绍如何阅读并了解详细的使用情况和费用
author: bandersmsft
manager: micflan
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: a68393b2852f8ddc758e2a47b9e1b5d94befb7b4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76290132"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>了解 Azure 使用情况和费用文件中的术语

详细的使用情况和费用文件包含指定时段的基于协商费率的每日额定使用量、购买（例如预留、市场费用）以及退款。
费用不包括额度、税费或者其他费用或折扣。
下表介绍每个帐户类型涉及的费用。

帐户类型 | Azure 使用情况 | 市场使用情况 | 购买 | 退款
--- | --- | --- | --- | ---
企业协议 (EA) | 是 | 是 | 是 | 否
Microsoft 客户协议 (MCA) | 是 | 是 | 是 | 是
即用即付 (PAYG) | 是 | 是 | 否 | 否

若要详细了解市场订单（也称外部服务），请参阅[了解 Azure 外部服务费用](understand-azure-marketplace-charges.md)。

有关下载说明，请参阅[如何获取 Azure 账单发票和每日使用数据](../manage/download-azure-invoice-daily-usage-date.md)。
可以在 Microsoft Excel 或其他电子表格应用程序中打开使用情况和费用 CSV 文件。

## <a name="list-of-terms-and-descriptions"></a>术语和说明列表

下表介绍在最新版 Azure 使用情况和费用文件中使用的重要术语。
此列表涵盖即用即付 (PAYG)、企业协议 (EA) 和 Microsoft 客户协议 (MCA) 帐户。

术语 | 帐户类型 | 说明
--- | --- | ---
AccountName | EA、PAYG | 显示 EA 注册帐户或 PAYG 计费帐户的名称。
AccountOwnerId<sup>1</sup> | EA、PAYG | EA 注册帐户或 PAYG 计费帐户的唯一标识符。
AdditionalInfo | 全部 | 服务特定的元数据。 例如，虚拟机的映像类型。
BillingAccountId<sup>1</sup> | 全部 | 根计费帐户的唯一标识符。
BillingAccountName | 全部 | 计费帐户的名称。
BillingCurrency | 全部 | 与计费帐户关联的货币。
BillingPeriod | EA、PAYG | 费用的计费周期。
BillingPeriodEndDate | 全部 | 计费周期的结束日期。
BillingPeriodStartDate | 全部 | 计费周期的开始日期。
BillingProfileId<sup>1</sup> | 全部 | EA 注册、PAYG 订阅、MCA 计费对象信息或 AWS 合并帐户的唯一标识符。
BillingProfileName | 全部 | EA 注册、PAYG 订阅、MCA 计费对象信息或 AWS 合并帐户的名称。
ChargeType | 全部 | 指示费用表示使用情况 (**Usage**)、购买 (**Purchase**) 还是退款 (**Refund**)。
ConsumedService | 全部 | 与费用相关联的服务的名称。
CostCenter<sup>1</sup> | EA、MCA | 为订阅定义的用于跟踪成本的成本中心（仅在启用计费周期适用于 MCA 帐户）。
成本 | EA、PAYG | 参见 CostInBillingCurrency。
CostInBillingCurrency | MCA | 在应用额度或税款之前按计费货币计算的费用成本。
CostInPricingCurrency | MCA | 在应用额度或税款之前按定价货币计算的费用成本。
货币 | EA、PAYG | 参见 BillingCurrency。
日期<sup>1</sup> | 全部 | 费用项目的使用或购买日期。
EffectivePrice | 全部 | 计费周期的混合单价。 混合价格以求平均值的方式消除了单价中出现的任何波动，例如，使用渐变分层时，某段时间的价格会随数量的增加而降低。
ExchangeRateDate | MCA | 确定汇率的日期。
ExchangeRatePricingToBilling | MCA | 将以定价货币计算的成本转换为以计费货币计算的成本时所使用的汇率。
频率 | 全部 | 指示某项费用是否会重复收取。 费用可以一次性收取 (**OneTime**)、按月或按年重复收取 (**Recurring**)，或者按使用情况收取 (**UsageBased**)。
InvoiceId | PAYG、MCA | 在发票 PDF 上列出的唯一单据 ID。
InvoiceSection | MCA | 参见 InvoiceSectionName。
InvoiceSectionId<sup>1</sup> | EA、MCA | EA 部门或 MCA 发票科目的唯一标识符。
InvoiceSectionName | EA、MCA | EA 部门或 MCA 发票科目的名称。
IsAzureCreditEligible | 全部 | 指示是否可以使用 Azure 额度来支付此费用（值：True、False）。
位置 | MCA | 资源运行时所在的数据中心位置。
MeterCategory | 全部 | 计量的分类类别的名称。 例如，“云服务”和“网络”。  
MeterId<sup>1</sup> | 全部 | 计量的唯一标识符。
MeterName | 全部 | 计量的名称。
MeterRegion | 全部 | 按位置定价的服务的数据中心位置的名称。 参见“位置”。
MeterSubCategory | 全部 | 计量子分类类别的名称。
OfferId<sup>1</sup> | 全部 | 购买的套餐的名称。
PartNumber<sup>1</sup> | EA、PAYG | 用于获取特定计量定价的标识符。
PlanName | EA、PAYG | 市场计划名称。
PreviousInvoiceId | MCA | 如果此行项为退款，则指原始发票。
PricingCurrency | MCA | 基于协商价格进行定价时所使用的货币。
Products | 全部 | 产品的名称。
ProductId<sup>1</sup> | MCA | 产品的唯一标识符。
ProductOrderId | 全部 | 产品订单的唯一标识符。
ProductOrderName | 全部 | 产品订单的唯一名称。
PublisherName | 全部 | 市场服务的发布者。
PublisherType | 全部 | 发布者的类型（值：**Azure**、**AWS**、**Marketplace**）。
数量 | 全部 | 购买或消耗的单位数。
预订 ID | EA、MCA | 购买的预留实例的唯一标识符。
ReservationName | EA、MCA | 购买的预留实例的名称。
ResourceGroup | 全部 | 资源所在的[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的名称。 并非所有费用都来自部署到资源组的资源。 没有资源组的费用将显示为 null/空、“其他”  或“不适用”  。
ResourceId<sup>1</sup> | 全部 | [Azure 资源管理器](https://docs.microsoft.com/rest/api/resources/resources)资源的唯一标识符。
ResourceLocation | 全部 | 资源运行时所在的数据中心位置。 参见“位置”。
ResourceName | EA、PAYG | 资源的名称。 并非所有费用都来所部署的资源。 没有资源类型的费用将显示为 null/空、“其他”  或“不适用”  。
ResourceType | MCA | 资源实例的类型。 并非所有费用都来所部署的资源。 没有资源类型的费用将显示为 null/空、“其他”  或“不适用”  。
ServiceFamily | MCA | 服务所属的服务系列。
ServiceInfo1 | 全部 | 服务特定的元数据。
ServiceInfo2 | 全部 | 旧字段，具有可选的特定于服务的元数据。
ServicePeriodEndDate | MCA | 定价周期的结束日期，该周期定义和锁定了所使用的或购买的服务的价格。
ServicePeriodStartDate | MCA | 定价周期的开始日期，该周期定义和锁定了所使用的或购买的服务的价格。
SubscriptionId<sup>1</sup> | 全部 | Azure 订阅的唯一标识符。
SubscriptionName | 全部 | Azure 订阅的名称。
标记<sup>1</sup> | 全部 | 分配给资源的标记。 不包括资源组标记。 可以用来将内部退款的成本进行分组或分发。 有关详细信息，请参阅[使用标记来组织 Azure 资源](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)。
术语 | 全部 | 显示套餐的有效期限。 例如：在使用预留实例时，它显示的“期限”为 12 个月。 对于一次性购买或定期购买，“期限”为 1 个月（SaaS、市场支持）。 这不适用于 Azure 消耗。
UnitOfMeasure | 全部 | 用于服务计费的度量单位。 例如，计算服务按小时计费。
UnitPrice | EA、PAYG | 费用的单价。

_<sup>**1**</sup>_ 用于生成单个成本记录的唯一 ID 的字段。

请注意，某些字段的帐户类型之间可能存在大小写和间距差异。
旧版即用即付使用情况文件通过不同的部分来说明报表和每日使用情况。

### <a name="list-of-terms-from-older-apis"></a>旧版 API 中的术语列表
下表将旧版 API 中使用的术语映射到了新术语。 有关说明，请参阅上表。

旧术语 | 新术语
--- | ---
ConsumedQuantity | 数量
IncludedQuantity | 空值
InstanceId | ResourceId
费率 | EffectivePrice
单位 | UnitOfMeasure
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>确保费用正确

若要深入了解详细的使用情况和费用，请阅读介绍如何了解[即用即付](review-individual-bill.md)或 [Microsoft 客户协议](review-customer-agreement-bill.md)发票的文档。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [查看和下载 Microsoft Azure 发票](download-azure-invoice.md)
- [查看和下载 Microsoft Azure 使用情况与费用](download-azure-daily-usage.md)

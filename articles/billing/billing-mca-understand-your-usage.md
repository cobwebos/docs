---
title: 了解 Microsoft 客户协议的条款 Azure 使用情况和费用 CSV |Microsoft Docs
description: 了解如何阅读并理解 Azure 使用情况和费用 CSV 计费配置文件的部分
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 8f71f42386ce49d4d7178cb03d28d74edacd7e39
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371302"
---
# <a name="understand-terms-on-your-azure-usage-and-charges-csv-for-a-microsoft-customer-agreement"></a>了解 Microsoft 客户协议的条款 Azure 使用情况和费用 CSV

本文适用于 Microsoft 客户协议向计费帐户。 [检查是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

Azure 使用情况和费用 CSV 文件包含当前计费期间的每日和测定仪级使用费。

若要获取 Azure 的使用量和费用文件，请参阅[查看和下载 Azure 使用情况和你的 Microsoft 客户协议的费用](billing-download-azure-daily-usage.md)。
它以逗号分隔值 (.csv) 文件格式提供，可以在电子表格应用程序中打开它。

使用费是订阅的总“月度”  费用。 使用费不考虑任何信用额度或折扣。

## <a name="changes-in-the-enterprise-agreement-azure-usage-and-charges-csv"></a>企业协议 Azure 使用情况和费用 CSV 中的更改

如果 EA 客户，你将注意到，Azure 使用情况 CSV 文件的计费配置文件中的条款不同条款 EA Azure 使用情况 CSV 文件中。 此处是 EA 使用情况与计费配置文件使用情况的条款的条款的映射：

| EA Azure 使用情况 CSV | Microsoft 客户协议 Azure 使用情况和费用 CSV |
| --- | --- |
| Date | date |
| 月份| date |
| 日期 | date |
| 年龄 | date |
| 产品 | product |
| 计量 ID | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice | <!-- this was highlighted -->
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| 标记 | 标记 |
| StoreServiceIdentifier | 不适用 |
| DepartmentName | invoiceSection | <!-- this was highlighted -->
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| resourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible | <!-- this was highlighted -->

<!-- TO DO: Marketplace CSV? -->

## <a name="detailed-terms-and-descriptions-in-your-azure-usage-and-charges-file"></a>详细的条款和 Azure 使用情况和费用文件中的说明

以下部分介绍了 Azure 使用情况和费用文件中所示的重要条款。

术语 | 描述
--- | ---
invoiceId | 发票 PDF 上列出的唯一的文档 ID
previousInvoiceId | 对原始发票如果此行项是退款引用
billingAccountName | 计费帐户的名称
billingAccountId | 计费帐户的根的唯一标识符
billingProfileId | 会产生开具发票费用计费配置文件的名称
billingProfileName | 会产生开具发票费用计费配置文件的唯一标识符
invoiceSectionId | 发票部分的唯一标识符
invoiceSectionName | 发票节的名称
costCenter | 定义用于跟踪成本 （仅在打开的计费周期中可用） 的订阅成本中心
billingPeriodStartDate | 为其生成发票的计费周期开始日期
billingPeriodEndDate | 为其生成发票的计费周期结束日期
servicePeriodStartDate | 分级周期的开始日期的已定义和锁定的已使用或购买服务的定价
servicePeriodEndDate | 分级周期的结束日期的已定义和锁定的已使用或购买服务的定价
date | 对于 Azure 和 Marketplace 基于使用量的费用，这是分级日期。 对于一次性购买 （预订、 Marketplace） 或固定周期性费用 （支持产品/服务），这是在购买日期。
serviceFamily | 服务所属的服务系列
productOrderId | 产品订单的唯一标识符
productOrderName | 产品订单的唯一名称
consumedService | 已使用的服务的名称
meterId | 计量的唯一标识符
meterName | 测定仪名称
meterCategory | 测定仪分类类别的名称。 例如，*云服务*，*网络*，等等。
meterSubCategory | 计量子分类类别的名称
meterRegion | 可用的服务测定仪的区域的名称。 标识某些定价的服务的基于数据中心位置的数据中心位置。
offer | 购买的产品/服务的名称
productId | 对于产品产生费用的唯一标识符
product | 产生费用的产品名称
订阅 ID | 产生费用的订阅的唯一标识符
subscriptionName | 产生费用的订阅的名称
reservationId | 购买的预订实例的唯一标识符
reservationName | 购买的预订实例的名称
publisherType | 发布服务器的类型 (值： firstParty，thirdPartyReseller，thirdPartyAgency)
publisherName | 对 Marketplace 服务的发布服务器
resourceGroupId | 与资源关联的资源组的唯一标识符
resourceGroupName | 与资源相关联的资源组的名称
resourceId | 资源实例的唯一标识符
resourceType | 资源实例的类型
resourceLocation | 标识资源正在其中运行的数据中心位置。
位置 | 如果为相同的区域配置了不同的资源位置的资源的标准化的位置
quantity | 购买或使用的单位数
unitOfMeasure | 对服务计费的度量单位。 例如，计算每小时对服务进行计费。
chargeType | 费用的类型。 值： <ul><li>AsCharged 用法：根据使用情况的一项 Azure 服务所产生的费用。 这包括针对由于保留实例，无需付费的 Vm 的使用情况。</li><li>AsCharged PurchaseMarketplace:从 Marketplace 购买内容的一次性或固定周期性费用</li><li>AsCharged UsageMarketplace:基于消耗量的单位收取的 Marketplace 服务费用</li></ul>
isAzureCreditEligible | 该标志指示是否对服务的费用有资格将为使用 Azure 信用额度支付 (值：True、 False)
serviceInfo1 | 特定于服务的元数据
serviceInfo2 | 旧字段，用于捕获可选的服务特定元数据
additionalInfo | 其他特定于服务的元数据。
标记 | 分配给资源标记

### <a name="how-do-i-make-sure-that-the-charges-in-my-azure-usage-and-charges-file-are-correct"></a>如何确保我的 Azure 使用情况和费用文件中的费用是正确？

如果你希望更多详细信息的使用情况明细的文件上的费用，请参阅[了解计费配置文件的发票费用](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查与 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [查看和下载 Microsoft Azure 发票](billing-download-azure-invoice.md)
- [查看和下载你的 Microsoft Azure 使用情况和费用](billing-download-azure-daily-usage.md)

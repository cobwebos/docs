---
title: Microsoft 客户协议 Azure 使用情况和费用文件术语
description: 了解如何阅读并理解计费对象信息的 Azure 使用情况和费用 CSV 的各个科目。
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: b48cc5d5a36a382909adb250dd76b4f5783a4340
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "75990900"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Microsoft 客户协议的 Azure 使用情况和费用文件中的术语

本文适用于 Microsoft 客户协议的计费帐户。 [检查你是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

Azure 使用情况和费用 CSV 文件包含当前计费周期的每日和测定仪级使用费。

若要获取 Azure 使用情况和费用文件，请参阅[查看和下载 Microsoft 客户协议的 Azure 使用情况与费用](download-azure-daily-usage.md)。 它以逗号分隔值 (.csv) 文件格式提供，可以在电子表格应用程序中打开它。

使用费是订阅的总“月度”  费用。 使用费不考虑任何信用额度或折扣。

## <a name="changes-from-azure-ea-usage-and-charges"></a>Azure EA 使用情况和费用的变化

如果你是 EA 客户，则会注意到 Azure 计费对象信息使用情况 CSV 文件中的术语不同于 Azure EA 使用情况 CSV 文件中的术语。 下面是从 EA 使用情况术语到计费对象信息使用情况术语的映射：

| Azure EA 使用情况 CSV | Microsoft 客户协议 Azure 使用情况和费用 CSV |
| --- | --- |
| Date | date |
| 月份| date |
| 日期 | date |
| 年龄 | date |
| Products | product |
| 计量 ID | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Tags | 标记 |
| StoreServiceIdentifier | 空值 |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>详细的术语和说明

以下术语显示在 Azure 使用情况和收费文件中。

术语 | 说明
--- | ---
invoiceId | 在发票 PDF 上列出的唯一单据 ID
previousInvoiceId | 如果此行项为退款，则指原始发票
billingAccountName | 计费帐户的名称
billingAccountId | 根计费帐户的唯一标识符
billingProfileId | 产生开票费用的计费对象信息的名称
billingProfileName | 产生开票费用的计费对象信息的唯一标识符
invoiceSectionId | 发票科目的唯一标识符
invoiceSectionName | 发票科目的名称
costCenter | 在订阅上定义的用于跟踪成本的成本中心（仅在启用计费周期提供）
billingPeriodStartDate | 生成发票的计费周期的开始日期
billingPeriodEndDate | 生成发票的计费周期的结束日期
servicePeriodStartDate | 定价周期的开始日期，该周期定义和锁定了所使用的或购买的服务的价格
servicePeriodEndDate | 定价周期的结束日期，该周期定义和锁定了所使用的或购买的服务的价格
date | 对于基于 Azure 和市场使用情况的费用，这是计价日期。 对于一次性购买（预留、市场）或固定的定期费用（支持套餐），这是购买日期。
serviceFamily | 服务所属的服务系列
productOrderId | 产品订单的唯一标识符
productOrderName | 产品订单的唯一名称
consumedService | 已耗用服务的名称
meterId | 计量的唯一标识符
meterName | 计量的名称
meterCategory | 计量的分类类别的名称。 例如，“云服务”、“网络”等。  
meterSubCategory | 计量子分类类别的名称
meterRegion | 提供服务计量的区域的名称。 指明某些服务的数据中心的位置，这些服务根据数据中心位置进行定价。
offer | 购买的套餐的名称
productId | 产生费用的产品的唯一标识符
product | 产生费用的产品的名称
订阅 ID | 产生费用的订阅的唯一标识符
subscriptionName | 产生费用的订阅的名称
reservationId | 购买的预留实例的唯一标识符
reservationName | 购买的预留实例的名称
publisherType | 发布者的类型（值：firstParty、thirdPartyReseller、thirdPartyAgency）
publisherName | 市场服务的发布者
resourceGroupId | 与资源关联的资源组的唯一标识符
resourceGroupName | 与资源关联的资源组的名称
resourceId | 资源实例的唯一标识符
resourceType | 资源实例的类型
resourceLocation | 指明资源正在其中运行的数据中心的位置。
location | 在为相同区域配置了不同资源位置的情况下，资源的规范化位置
quantity | 购买或消耗的单位数
unitOfMeasure | 用于服务计费的度量单位。 例如，计算服务按小时计费。
chargeType | 费用类型。 值： <ul><li>AsCharged-Usage：根据 Azure 服务使用情况产生的费用。 这包括因为预留实例的缘故而未收费的 VM 使用情况。</li><li>AsCharged-PurchaseMarketplace：针对市场购买的一次性费用或固定的定期费用</li><li>AsCharged-UsageMarketplace：按消耗单位数计费的市场服务费用</li></ul>
isAzureCreditEligible | 一个标记，指示是否可以使用 Azure 额度来支付此服务费用（值：True、False）
serviceInfo1 | 特定于服务的元数据
serviceInfo2 | 旧字段，用于捕获可选的服务特定元数据
additionalInfo | 其他特定于服务的元数据。
标记 | 分配给资源的标记

### <a name="make-sure-that-charges-are-correct"></a>确保费用正确

若要确保详细使用情况文件中的费用正确，可以验证它们。 请参阅[了解计费对象信息的发票上的费用](review-customer-agreement-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [查看和下载 Microsoft Azure 发票](download-azure-invoice.md)
- [查看和下载 Microsoft Azure 使用情况与费用](download-azure-daily-usage.md)

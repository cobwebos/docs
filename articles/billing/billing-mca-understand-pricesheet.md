---
title: 了解 Microsoft 客户协议的价目表中的术语 - Azure
description: 了解如何阅读并理解 Microsoft 客户协议的使用情况和账单。
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 4d83228fbec395d604e5ce3f988d2a6157f21eed
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490661"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Microsoft 客户协议价目表中的术语

本文适用于 Microsoft 客户协议的 Azure 计费帐户。 [检查你是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

如果你是计费对象信息所有者、参与者、读者或发票管理者，则可从 Azure 门户下载组织的价目表。 请参阅[查看和下载组织的定价](billing-ea-pricing.md)。

## <a name="terms-and-descriptions-in-your-price-sheet"></a>价目表中的术语和说明

以下部分介绍在 Microsoft 客户协议价目表中显示的重要术语。

| **字段名称**   | **说明**   |
| --- | --- |
| billingAccountId  | 计费帐户的唯一标识符。   |
| billingAccountName  | 计费帐户的名称。  |
| billingProfileId  | 计费对象信息的唯一标识符。   |
| billingProfileName  | 设置为接收发票的计费对象信息的名称。 价目表中与此计费对象信息关联的价格。 |
| productOrderName  | 购买的产品计划的名称。 |
| serviceFamily  | Azure 服务的类型。例如：计算、分析、安全性 |
| Products  | 产生费用的产品的名称。例如：基本 SQL DB 与标准 SQL DB  |
| productId  | 其计量已消耗的产品的唯一标识符。 |
| unitOfMeasure  | 确定用于服务计费的度量单位。 例如，计算服务按小时计费。 |
| meterId  | 计量的唯一标识符。 |
| meterName  | 计量的名称。 此计量表示 Azure 服务的可部署资源。 |
| meterCategory  | 计量的分类类别的名称。 例如，“云服务”、“网络”等。   |
| meterType  |  计量类型的名称。 |
| meterSubCategory  | 计量子分类类别的名称。  |
| meterRegion  | 提供服务计量的区域的名称。 指明某些服务的数据中心的位置，这些服务根据数据中心位置进行定价。    |
| tierId  | 在适用的情况下标识定价层。 当价格因消耗的单位数而异时，此项可以与 tierMinimumUnits 配合使用来设置分层的价格。    |
| tierMinimumUnits  | 定义层级范围的下限，这是定义价格的依据。 例如，如果范围为 0 到 100，则 tierMinimumUnits 为 0。  |
| effectiveStartDate  | 价格生效的开始日期。 |
| effectiveEndDate  | 有效价格的结束日期。 |
| unitPrice  | 计费时的单价（不是有效的混合价），特定于计量和产品订单名称。  注意：如果服务的不同层级中存在价格差异，则单价不同于使用情况详细信息下载内容中的有效价格。  如果服务使用多层级定价，则有效价格是不同层级的混合费率，不显示特定于层级的单价。 混合价格或有效价格是针对多个层级（其中的每个层级有具体的单价）消耗的数量得出的净价格。 |
| basePrice  | 客户签署时的市场价格，或者服务计量启动时（如果是在登录后启动）的市场价格。   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [查看和下载组织的定价](billing-ea-pricing.md)

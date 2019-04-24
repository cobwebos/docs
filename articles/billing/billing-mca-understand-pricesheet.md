---
title: 了解 Microsoft 的客户协议的 Azure 价格表中的条款 |Microsoft Docs
description: 了解如何阅读并理解 Azure 订阅的使用情况和帐单
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: eb6184e10d38cdcfad7070663e36f6610d009cdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371353"
---
# <a name="understand-the-terms-in-your-price-sheet-for-a-microsoft-customer-agreement"></a>了解 Microsoft 客户协议的价格表中的条款

本文适用于 Microsoft 客户协议向计费帐户。 [检查是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

如果您是所有者、 参与者、 读取器或发票管理器的计费配置文件可以从 Azure 门户中下载你组织的价目表。 请参阅[视图和下载你的组织的定价](billing-ea-pricing.md)。

## <a name="detailed-terms-and-descriptions-in-your-microsoft-customer-agreement-price-sheet"></a>详细的条款和说明在你的 Microsoft 客户协议价格表中

以下部分介绍了在你的 Microsoft 客户协议价格表中显示的重要条款。

| **字段名称**   | **说明**   |
| --- | --- |
| billingAccountId  | 计费帐户的唯一标识符。   |
| billingAccountName  | 计费帐户的名称。  |
| billingProfileId  | 有关计费的配置文件的唯一标识符。   |
| billingProfileName  | 设置为接收发票计费配置文件的名称。 在价目表中的价格是与此计费的配置文件相关联。 |
| productOrderName  | 已购买的产品计划的名称。 |
| serviceFamily  | Azure 服务的类型。例如：计算、 分析、 安全 |
| 产品  | 产生费用的产品名称。例如：SQL DB 在标准 SQL 数据库  |
| productId  | 使用的计量的产品的唯一标识符。 |
| unitOfMeasure  | 标识对服务计费的度量的单位。 例如，计算每小时对服务进行计费。 |
| meterId  | 测定仪的唯一标识符。 |
| meterName  | 计量的名称。 指标表示一项 Azure 服务的可部署资源。 |
| meterCategory  | 测定仪分类类别的名称。 例如，_云服务_，_网络_，等等。 |
| meterType  |  计量类型的名称。 |
| meterSubCategory  | 计量子分类类别的名称。  |
| meterRegion  | 可用的服务测定仪的区域的名称。 指明某些服务的数据中心的位置，这些服务根据数据中心位置进行定价。    |
| tierId  | 标识时适用的定价层。 这可以与用于分层的价格价格而有所不同时基于在上设置使用的单位数 tierMinimumUnits 一起使用。    |
| tierMinimumUnits  | 定义为其定义价格层范围的下限。 例如，如果该范围是 0 到 100，tierMinimumUnits 将为 0。  |
| effectiveStartDate  | 开始价格生效的日期。 |
| effectiveEndDate  | 有效的价格的结束日期。 |
| unitPrice  | 每个单位时的计费 （不有效混合的价格） 的价格为特定于计量，并在产品的订单名称。  请注意:单位价格与不相同时在层之间的差异的价格的服务下载使用情况详细信息中的有效价格。  如果使用多层定价的服务，发生有效价格跨层是混合型的速率并不会显示特定于层的单价。 混合的价格或有效的价格是已使用的数量可以扩展到多个层 （其中每个层都有特定的单价） 的 net 价格。 |
| basePrice  | 客户登录时市场价格或市场价格时服务测定仪启动如果晚于单一登录。   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查与 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [查看和下载你的组织定价](billing-ea-pricing.md)

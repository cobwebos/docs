---
title: 了解 Azure 帐单 |Microsoft Docs
description: 了解如何阅读并理解 Azure 订阅的使用情况和帐单
services: ''
documentationcenter: ''
author: tonguyen10
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
ms.openlocfilehash: 4303b79a7ee69d029504bf6ca2359f6e6070e5b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370104"
---
# <a name="understand-your-microsoft-azure-bill"></a>了解 Microsoft Azure 帐单
要了解 Azure 帐单，请在 Azure 门户中将发票与每日使用情况明细文件和成本管理报表进行比较。

本文不适用于签署企业协议的 Azure 客户（EA 客户）。 如果你是 EA 客户，请参阅[具有企业协议的 Azure 客户了解帐单](billing-understand-your-bill-ea.md)。

本文不能应用于与 Microsoft 客户协议的 Azure 客户。 如果 Microsoft 客户协议的计费帐户，请参阅[了解 Microsoft 客户协议发票上的 Azure 费用](billing-mca-understand-your-bill.md)。

有关 Azure 云解决方案提供商 (Azure CSP) 计划中的计费原理的说明（包括结算周期，定价和使用情况），请参阅 [Azure CSP 计费概述](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/)。

## <a name="charges"></a>查看费用

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

如果发票上有需要详细了解的费用，可以通过使用情况文件或 Azure 门户来比较使用情况和成本。

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>选项 1：通过使用情况文件比较使用情况和成本

使用情况明细 CSV 文件按计费周期和每日使用情况显示费用信息。 若要获取此文件，请参阅[获取 Azure 计费发票和每日使用数据](billing-download-azure-invoice-daily-usage-date.md)。

在计量级别显示使用费。 下方的术语在发票和使用情况明细文件中表示同一事物。 例如，发票中的计费周期等同于使用情况明细文件中的计费周期。

 | 发票（PDF） | 使用情况明细 (CSV)|
 | --- | --- |
|计费周期 | 计费周期 |
 |Name |测定仪类别 |
 |Type |测定仪子类别 |
 |Resource |测定仪名称 |
 |区域 |测定仪区域 |
 |已耗用 |已耗用数量 |
 |附送 |附送数量 |
 |可计费 |超额数量 |

发票的“使用费”部分包含计费周期内已使用的每种计量的总额  。 例如，以下屏幕截图显示的是 Azure 计划程序服务的使用费。

![发票使用费](./media/billing-understand-your-bill/1.png)

在使用情况明细 CSV 的“帐目报告”部分也显示了同样的费用  。 “已使用”量和“值”都与发票上的数据匹配   。

![CSV 使用费](./media/billing-understand-your-bill/2.png)

若要查看此费用的每日明细，请转到 CSV 的“每日使用情况”部分  。 筛选*计划程序*下*计量类别*。 可以查看计量的使用日期和使用量。 表中也列出“资源”和“资源组”信息，以供比较   。 “已使用”值的总和应与该发票上显示的数据相同  。

![CSV 的“每日使用情况”部分](./media/billing-understand-your-bill/3.png)

若要获取每日成本，请将“报表”部分中的“已使用”量与“费率”值相乘    。

若要了解更多信息，请参阅以下文章：

- [了解 Azure 发票](billing-understand-your-invoice.md)
- [了解 Azure 的详细使用情况](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>选项 2：使用情况和在 Azure 门户中的成本相比较

还可以通过 Azure 门户验证费用。 若要快速了解发票上的使用情况和费用，请查看成本管理图表。

1. 在 Azure 门户中，转到 [订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 选择你的订阅 >“成本分析”。 
1. 按“时间范围”筛选。 
1. 若要继续以前的示例，请查看 Azure 计划程序服务的使用费。

   ![Azure 门户中成本分析视图](./media/billing-understand-your-bill/4.png)

1. 选择该行即可查看每日成本明细。

   ![Azure 门户中的成本历史记录视图](./media/billing-understand-your-bill/5.png)

有关详细信息，请参阅[通过 Azure 计费和成本管理防止意外成本](billing-getting-started.md#costs)。

## <a name="external"></a>外部服务单独计费

外部服务或市场费用针对的是由第三方软件供应商创建的资源。 这些资源可以通过 Azure 市场获取。 例如，Barracuda 防火墙是由第三方提供的 Azure 市场资源。 此防火墙的所有费用及其对应的计量指标都将显示为外部服务费用。

外部服务单独计费。 Azure 发票上不会记录该费用。 若要了解详细信息，请参阅[了解 Azure 外部服务收费](billing-understand-your-azure-marketplace-charges.md)。

## <a name="resources-billed-by-usage-meters"></a>资源按使用计量计费

Azure 不直接根据资源成本计费。 资源费用按一个或多个计量来计算。 指标用于跟踪其整个生存期内的资源的使用情况。 随后会使用这些计量指标来计算费用。

例如，创建单个 Azure 资源（例如虚拟机）时，会创建一个或多个计量实例。 指标用于跟踪一段时间内对资源的使用。 每个计量都会发送使用情况记录，这些记录由 Azure 用来计算账单费用。

例如，如果在 Azure 中创建了一个虚拟机 (VM)，那么可能会创建以下计量指标来跟踪其使用情况：

- 计算小时数
- IP 地址小时数
- 入站数据传输
- 出站数据传输
- 标准托管磁盘
- 标准托管磁盘操作数
- 标准 IO 磁盘
- 标准 IO 块 Blob 读取
- 标准 IO 块 Blob 写入
- 标准 IO 块 Blob 删除

创建 VM 时，每种计量开始发出使用情况记录。 此使用情况和计量的价格在 Azure 计量系统中进行跟踪。

## <a name="payment"></a>支付账单

如果已将信用卡或借记卡设置为付款方式，则会在计费周期结束后的 10 天内自动进行付款。 在信用卡对帐单上，行项会显示 **MSFT Azure**。

若要更改用于付款的信用卡或借记卡，请参阅[添加、更新或删除用于 Azure 的信用卡或借记卡](billing-how-to-change-credit-card.md)。

如果[按发票付款](billing-how-to-pay-by-invoice.md)，可将款项寄送到发票底部列出的地点。

若要检查您的付款状态[创建支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。


## <a name="tips-for-cost-management"></a>成本管理建议

- 通过以下方式估算成本：
  - [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)
  - [总拥有成本计算器](https://aka.ms/azure-tco-calculator)
  - [每项服务的详细定价信息](https://azure.microsoft.com/pricing/)
- [在 Azure 门户中定期查看使用情况和成本](billing-getting-started.md#costs)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="learn-more"></a>了解详细信息

- [获取 Azure 计费发票和每日使用数据](billing-download-azure-invoice-daily-usage-date.md)
- [了解 Microsoft Azure 发票的条款](billing-understand-your-invoice.md)
- [了解 Microsoft Azure 详细使用条款](billing-understand-your-usage.md)
- [Azure 门户成本管理](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [通过 Azure 计费和成本管理来防止意外成本](billing-getting-started.md#costs)

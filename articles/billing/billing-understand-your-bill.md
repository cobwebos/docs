---
title: 了解 Azure 帐单 | Microsoft Docs
description: 了解如何阅读并理解 Azure 订阅的使用情况和帐单
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: tonguyen
ms.openlocfilehash: 689ea9e0d029bb65bc579fc914c6ed3073b4a96b
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064292"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>了解 Microsoft Azure 帐单
要了解 Azure 帐单，请在 Azure 门户中将发票与每日使用情况明细文件和成本管理报表进行比较。

>[!NOTE]
>本文不适用于企业协议 (EA) 客户。 对于 EA 客户，[可在企业门户找到发票文档。](https://ea.azure.com/helpdocs/understandingYourInvoice)  

若要获取发票 PDF 并下载每日使用情况明细文件 CSV 副本，请参阅[获取 Azure 帐单和每日使用情况数据](billing-download-azure-invoice-daily-usage-date.md)。 

有关发票和每日使用情况明细文件的详细条款和说明，请参阅[了解 Microsoft Azure 发票的相关条款](billing-understand-your-invoice.md)和[了解 Microsoft Azure 使用情况明细的相关条款](billing-understand-your-usage.md)。 

有关成本管理报表的详细信息，请参阅 [Azure 门户成本管理](https://docs.microsoft.com/azure/billing/billing-getting-started)。

## <a name="charges"></a>如何确定发票中的费用是正确的？

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

如果想要更详细地了解发票上的费用，可以选择以下几种方式。

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>选项 1：查看发票，并将使用情况和成本与使用情况明细 CSV 文件相比较

使用情况明细 CSV 文件按计费周期和每日使用情况显示费用信息。 若要获取使用情况明细 CSV 文件，请参阅[获取 Azure 帐单发票和每日使用情况数据](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date)。

在计量级别显示使用费。 下方的术语在发票和使用情况明细文件中表示同一事物。 例如，发票中的计费周期等同于使用情况明细文件中的计费周期。

 | 发票（PDF） | 使用情况明细 (CSV)|
 | --- | --- |
|计费周期 | 计费周期 |
 |名称 |测定仪类别 |
 |Type |测定仪子类别 |
 |资源 |测定仪名称 |
 |区域 |测定仪区域 |
 |已耗用 |已耗用数量 |
 |附送 |附送数量 |
 |可计费 |超额数量 |

发票的“使用费”部分包含计费周期内已使用的每种计量的总额。 例如，以下屏幕截图显示的是 Azure 计划程序服务的使用费。

![发票使用费](./media/billing-understand-your-bill/1.png)

在使用情况明细 CSV 的“帐目报告”部分也显示了同样的费用。 “已使用”量和“值”都与发票上的数据匹配。

![CSV 使用费](./media/billing-understand-your-bill/2.png)

若要每日查看此费用明细，请转到 CSV 的“每日使用情况”部分。 通过筛选“计量类别”下的“计划程序”查看计量的使用日期和使用量。 表中也列出“资源”和“资源组”信息，以供比较。 “已使用”值的总和应与该发票上显示的数据相同。

![CSV 的“每日使用情况”部分](./media/billing-understand-your-bill/3.png)

若要获取每日成本，请将“报表”部分中的“已使用”量与“费率”值相乘。

若要了解有关发票的详细信息，请参阅[了解 Azure 发票](billing-understand-your-invoice.md)。

若要了解有关 CSV 中每一列的信息，请参阅[了解 Azure 的使用情况明细](billing-understand-your-invoice.md)。

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>选项 2：查看发票，在 Azure 门户中将其与使用情况和成本相比较

Azure 门户也可帮助核查费用。Azure 门户提供费用管理图表，可供快速了解发票上的使用情况和费用。

若要继续查看上文中的示例，请访问[订阅页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，选择订阅，然后选择“成本分析”。 在该处，可以指定时间范围并查看 Azure 计划程序服务的使用费。

![Azure 门户中成本分析视图](./media/billing-understand-your-bill/4.png)

若要查看“成本历史记录”中的每日成本明细，请单击该行。

![Azure 门户中的成本历史记录视图](./media/billing-understand-your-bill/5.png)

有关详细信息，请参阅[通过 Azure 计费和成本管理防止意外成本](billing-getting-started.md#costs)。

## <a name="external"></a>外部服务如何收费？
外部服务（也称为 Azure 市场订单）由独立的服务供应商提供，需要单独计费。 Azure 发票上不会记录该费用。 若要了解详细信息，请参阅[了解 Azure 外部服务收费](billing-understand-your-azure-marketplace-charges.md)。

## <a name="payment"></a>如何付款？

如果已将信用卡或借记卡设置为付款方式，则会在计费周期结束后的 10 天内自动进行付款。 在信用卡对帐单上，行项会显示 **MSFT Azure**。

如果[通过发票付款](billing-how-to-pay-by-invoice.md)，可将付款寄送到发票底部列出的地点。 有关更多帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>如何查看通过信用卡付款时的付款状态？

[创建支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以询问付款状态。 

## <a name="are-there-different-azure-customer-types-how-do-i-know-what-customer-type-i-am"></a>是否存在不同的 Azure 客户类型？ 如何识别自己的客户类型？
存在不同的 Azure 客户类型。 为了更好地理解自己的定价和计费情况，请查看以下客户类型描述。

- **企业**：企业客户与 Azure 签订了企业协议，以达成货币承诺并获取自定义 Azure 资源定价的权限。
- **Web 直接**：Web 直接客户没有与 Azure 签订任何自定义协议。 此类客户通过 azure.com 注册 Azure，并接受所有 Azure 资源对外公布的价格。
- **云服务提供商**：云服务提供商通常是最终客户雇用的公司，他们负责在 Azure 的基础上生成解决方案。

## <a name="why-dont-i-see-the-cost-the-resource-i-have-created-in-my-bill"></a>我为什么没有在帐单中看到自己创建的资源的费用？
Azure 不会直接基于资源费用进行计费。 计费工作基于一个或多个计量指标，这些指标用于跟踪资源整个生存期的使用情况。 随后会使用这些计量指标来计算费用。 请参阅以下内容，了解更多关于 Azure 计量指标的信息。

## <a name="how-does-azure-charge-metering-work"></a>Azure 计费计量指标如何工作？
启用单个 Azure 资源（例如虚拟机）时，同时也会创建一个或多个计量指标实例。 这些计量指标可跟踪资源的使用情况。 每个计量指标都会发送使用情况记录，Azure 随后会在费用计量系统中使用这些记录来计算费用。 

例如，如果在 Azure 中创建了一个虚拟机，那么可能会创建以下计量指标来跟踪其使用情况：

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

创建虚拟机后，以上所有计量指标都会开始发送使用情况记录。 Azure 计量系统随后会使用这些使用情况记录和计量指标的价格来确定向客户收取的费用。

> [!Note]
> 上面的计量指标示例可能仅仅是所创建的 VM 的计量指标的一个子集。

## <a name="what-is-the-difference-between-azure-1st-party-charges-and-azure-marketplace-charges"></a>Azure 第一方费用和 Azure 市场费用的区别是什么？
Azure 第一方费用针对的是直接由 Azure 开发并提供的资源。 Azure 市场费用针对的是由第三方软件提供商创建的资源（这些资源可以通过 Azure 市场获取）。 例如，Barracuda 防火墙是由第三方提供的 Azure 市场资源。 此防火墙的所有费用及其对应的计量指标都将显示为市场费用。 

## <a name="tips-for-cost-management"></a>成本管理建议
- 通过使用[定价计算器](https://azure.microsoft.com/pricing/calculator/)和[总拥有成本计算器](https://aka.ms/azure-tco-calculator)估计成本，获取[每项服务的定价信息明细](https://azure.microsoft.com/pricing/)。
- [设置计费警报](billing-set-up-alerts.md)。
- [在 Azure 门户中定期查看使用情况和成本](billing-getting-started.md#costs)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

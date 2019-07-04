---
title: 了解在 Azure 中的 Microsoft 客户协议发票
description: 了解如何阅读并了解你在 Azure 中的 Microsoft 客户协议帐单
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
ms.openlocfilehash: fed658d3f672d6116d7c2b0f3e2e9ad989dd67c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490634"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>在你的 Microsoft 客户协议发票的条款

本文适用于 Azure 的计费帐户 Microsoft 客户协议。 [检查是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

发票提供费用和有关付款的说明的摘要。 可以从 [Azure 门户](https://portal.azure.com/)下载可移植文档格式 (.pdf) 的发票，也可通过电子邮件获取。 有关详细信息，请参阅[视图和下载 Microsoft Azure 发票](billing-download-azure-invoice.md)。

## <a name="billing-period"></a>计费周期

你每月开具发票。 您可以查找了哪一天的月份通过检查接收发票*发票日期*计费中的配置文件属性下[Azure 门户](https://portal.azure.com/)。 在计费周期的结束和发票日期之间发生的费用包括在下个月的发票，因为它们是在下一计费周期中。 发票 PDF 上面列出了计费期间开始和结束日期为每张发票**帐单摘要**。

## <a name="invoice-terms-and-descriptions"></a>发票的条款和说明

以下各节列表的重要术语在发票上看到并提供每个字词的说明。

### <a name="invoice-summary"></a>发票汇总

**发票汇总**第一页的顶部和显示有关你计费的配置文件和支付方式信息。

![发票摘要部分](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| 术语 | 描述 |
| --- | --- |
| 买方 |在计费帐户属性中找到你法律实体的地址|
| 帐单邮寄地址 |帐单地址接收的发票，在计费的配置文件属性中找到的计费配置文件|
| 计费对象信息 |收到发票计费配置文件的名称 |
| P.O. 数字 |可选采购订单号，由你分配用于跟踪 |
| 发票号 |用于跟踪用途唯一的、 由 Microsoft 生成发票编号 |
| 发票日期 |生成发票，通常在计费周期结束后的 5 到 12 天的日期。 您可以检查你的发票日期计费的配置文件属性中。|
| 付款条款 |如何支付你的 Microsoft 帐单。 *Net 30 天*意味着在发票日期的 30 天内需要付费。 |

### <a name="billing-summary"></a>计费摘要

**计费摘要**显示自上一计费周期、 应用了任何信用额度、 税款和总金额的费用根据计费的配置文件到期。

![帐单摘要部分](./media/billing-understand-your-invoice-mca/billingsummary.png)

| 术语 | 描述 |
| --- | --- |
| Charges|此计费的配置文件，因为最后一个计费周期的 Microsoft 收费的总数 |
| 致谢 |返回从收到的信用额度 |
| 应用的 azure 额度 | Azure 额度，将自动应用于 Azure 的费用每个计费周期 |
| 小计 |到期税前金额 |
| 税务 |类型和量付费，具体取决于你计费的配置文件的国家/地区的税务。 如果不需要交税，你不会看到税务发票上。 |
| 估计的总节省金额 |估计的总金额，用于保存从有效的折扣。 如果适用，有效的折扣率按发票部分列出下购买行项详细信息中。 |

### <a name="invoice-sections"></a>发票部分

对于每个计费配置文件下发票部分中，您将看到费用、 应用的 Azure 信用额度量、 税款和总应付金额。

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>发票部分的详细信息

详细信息显示每个发票部分按产品订购细分的成本。 在每个产品顺序，成本细分的服务的类型。 您可以找到每日费用将产品和服务在 Azure 门户和 Azure 使用情况和费用 CSV。 若要了解详细，请参阅[了解发票上的费用的 Microsoft 客户协议](billing-mca-understand-your-bill.md)。

每个服务系列计算通过减去总应付金额*Azure 额度*从*信用额度/费用*并添加*税务*:


![发票部分的详细信息](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| 术语 |描述 |
| --- | --- |
| 单位价格 | 服务 （定价货币），它是有效的单位价格用于速率使用情况。 这是唯一的产品、 服务系列、 测定仪和产品/服务。 |
| Qty | 购买或计费周期内使用数量 |
| 费用/信用额度 | 费用应用信用额度/退款后的净额 |
| Azure 额度 | 应用于费用/信用额度的 Azure 信用额度的量|
| 税率 | 具体取决于国家/地区的税务费率 |
| 税额 | 根据税率的应用购买的税量 |
| 总计 | 购买结余的总金额 |

### <a name="how-to-pay"></a>如何支付

发票底部有電話你的帐单的说明。 你可以通过检查，在线，付费或联机。 如果在线支付，你可以使用信用卡/借记卡或 Azure 信用额度，如果适用。

### <a name="publisher-information"></a>发布者信息

如果你在你的帐单中的第三方服务，是你的发票底部列出的名称和每个发布服务器的地址。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查与 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [了解计费配置文件的发票费用](billing-mca-understand-your-bill.md)
- [如何获取 Azure 计费发票和每日使用数据](billing-download-azure-invoice-daily-usage-date.md)
- [查看你组织的 Azure 定价](billing-ea-pricing.md)
- [你计费的配置文件的的视图税务文档](billing-mca-download-tax-document.md)

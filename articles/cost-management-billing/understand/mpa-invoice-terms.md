---
title: 了解 Azure 中的 Microsoft 合作伙伴协议发票
description: 了解如何阅读并理解 Azure 中的 Microsoft 合作伙伴协议帐单
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2020
ms.author: banders
ms.openlocfilehash: fcebe02d62beba1e48759c0298a18b26f3a61cbf
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133312"
---
# <a name="terms-in-your-microsoft-partner-agreement-invoice"></a>Microsoft 合作伙伴协议发票中的术语

本文适用于 Microsoft 合作伙伴协议的 Azure 计费帐户。 [检查你是否有权访问 Microsoft 合作伙伴协议](#check-access-to-a-microsoft-partner-agreement)。

发票提供了收费摘要和有关付款的说明。 可以从 [Azure 门户](https://portal.azure.com/)下载可移植文档格式 (.pdf) 的发票，也可通过电子邮件获取。 有关详细信息，请参阅[查看和下载 Microsoft Azure 发票](download-azure-invoice.md)。

下列部分列出了发票上显示的重要术语，并提供了每个术语的说明。

## <a name="billing-period"></a>计费周期

每月开票一次。 在计费周期结束和发票日期之间产生的费用包括在下月的发票中，因为它们属于下一计费周期。 每个发票的计费周期开始日期和结束日期列在发票 PDF 中的“计费摘要”上方。 

## <a name="invoice-summary"></a>发票摘要

“发票摘要”位于第一页顶部，显示了有关计费对象信息和付款方式的信息。 

<!-- add screenshot -->

| 术语 | 说明 |
| --- | --- |
| 买方 |法律实体的地址，位于计费帐户属性中|
| 帐单邮寄地址 |接收发票的计费对象信息的帐单地址，位于计费对象信息属性中|
| 计费对象信息 |接收发票的计费对象信息的名称 |
| P.O. 数字 |可选采购订单号，由你分配用于跟踪 |
| 发票号 |由 Microsoft 生成的唯一发票号，用于跟踪 |
| 发票日期 |生成发票的日期，通常是计费周期截止后的 5 到 12 天。 可以在计费对象信息属性中查看发票日期。|
| 付款条款 |如何支付 Microsoft 账单。 “净 60 天”是指在开票日期后的 60 天内付款。  |

## <a name="billing-summary"></a>计费摘要

“计费摘要”显示  自上一个计费周期以来针对计费对象信息收取的费用、应用的任何额度、税款，以及应付总金额。

<!-- add screenshot -->

| 术语 | 说明 |
| --- | --- |
| Charges|自上次计费周期以来针对此计费对象信息收取的 Microsoft 费用总数目 |
| 致谢 |从退货收到的额度 |
| 已应用的 Azure 额度 | 每个计费周期自动应用到 Azure 费用的 Azure 额度 |
| 小计 |应付的税前金额 |
| 税款 |需支付的税款的类型和金额，具体取决于计费对象信息的国家/地区。 如果不需缴税，则发票上看不到税款。 |
| 估计节省总额 |估计的通过有效折扣获得的节省总额。 在适用情况下，有效折扣率按“发票科目”列在“详细信息”中购买行项的下面。 |


## <a name="billing-details-by-product"></a>按产品列出的计费详细信息

**按产品列出的计费详细信息**部分列出了与此计费对象信息关联的每个产品的总费用。 可以在 Azure 使用情况和费用 CSV 中查看每个产品费用的每日明细。 若要了解如何将发票 pdf 与 Azure 使用情况和费用 CSV 进行对帐，请参阅[了解 Microsoft 合作伙伴协议帐单](review-partner-agreement-bill.md)。

## <a name="how-to-pay"></a>如何支付

在发票底部有账单付款说明。 可以在发票日期后 60 天内用支票或电汇付款。

## <a name="publisher-information"></a>发布者信息

如果账单中有第三方服务，则每个发布者的名称和地址列在发票底部。

## <a name="check-access-to-a-microsoft-partner-agreement"></a>检查对 Microsoft 合作伙伴协议的访问权限
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [了解计费对象信息的发票上的费用](review-customer-agreement-bill.md)
- [如何获取 Azure 计费发票和每日使用数据](../manage/download-azure-invoice-daily-usage-date.md)
- [查看组织的 Azure 定价](../manage/ea-pricing.md)
- [查看计费对象信息的税单](mca-download-tax-document.md)

---
title: 了解 Azure 中的 Microsoft 客户协议发票
description: 了解如何阅读并理解 Azure 中的 Microsoft 客户协议账单
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: banders
ms.openlocfilehash: 4c4b105bb4b67e855de27b6a57d2080fde068421
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "79237898"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Microsoft 客户协议发票中的术语

本文适用于 Microsoft 客户协议的 Azure 计费帐户。 [检查你是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

发票提供了收费摘要和有关付款的说明。 可以从 [Azure 门户](https://portal.azure.com/)下载可移植文档格式 (.pdf) 的发票，也可通过电子邮件获取。 有关详细信息，请参阅[查看和下载 Microsoft Azure 发票](download-azure-invoice.md)。

## <a name="billing-period"></a>计费周期

每月开票一次。 若要了解你在每月的哪一天接收发票，可在 [Azure 门户](https://portal.azure.com/)的计费对象信息属性下查看“发票日期”。 在计费周期结束和发票日期之间产生的费用包括在下月的发票中，因为它们属于下一计费周期。 每个发票的计费周期开始日期和结束日期列在发票 PDF 中的“计费摘要”上方。 

## <a name="invoice-terms-and-descriptions"></a>发票的术语和说明

下列部分列出了发票上显示的重要术语，并提供了每个术语的说明。

### <a name="invoice-summary"></a>发票摘要

“发票摘要”位于第一页顶部，显示了有关计费对象信息和付款方式的信息。 

![发票摘要部分](./media/mca-understand-your-invoice/invoicesummary.png)

| 术语 | 说明 |
| --- | --- |
| 买方 |法律实体的地址，位于计费帐户属性中|
| 帐单邮寄地址 |接收发票的计费对象信息的帐单地址，位于计费对象信息属性中|
| 计费对象信息 |接收发票的计费对象信息的名称 |
| P.O. 数字 |可选采购订单号，由你分配用于跟踪 |
| 发票号 |由 Microsoft 生成的唯一发票号，用于跟踪 |
| 发票日期 |生成发票的日期，通常是计费周期截止后的 5 到 12 天。 可以在计费对象信息属性中查看发票日期。|
| 付款条款 |如何支付 Microsoft 账单。 “净 30 天”是指在开票日期后的 30 天内付款。  |

### <a name="billing-summary"></a>计费摘要

“计费摘要”显示  自上一个计费周期以来针对计费对象信息收取的费用、应用的任何额度、税款，以及应付总金额。

![计费摘要部分](./media/mca-understand-your-invoice/billingsummary.png)

| 术语 | 说明 |
| --- | --- |
| Charges|自上次计费周期以来针对此计费对象信息收取的 Microsoft 费用总数目 |
| 致谢 |从退货收到的额度 |
| 已应用的 Azure 额度 | 每个计费周期自动应用到 Azure 费用的 Azure 额度 |
| 小计 |应付的税前金额 |
| 税款 |需支付的税款的类型和金额，具体取决于计费对象信息的国家/地区。 如果不需缴税，则发票上看不到税款。 |
| 估计节省总额 |估计的通过有效折扣获得的节省总额。 在适用情况下，有效折扣率按“发票科目”列在“详细信息”中购买行项的下面。 |

### <a name="invoice-sections"></a>发票科目

对于计费对象信息下的每个发票科目，你会看到费用、应用的 Azure 额度的金额，税款，以及应付总金额。

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>按发票科目列出的详细信息

详细信息显示按产品订单细分的每个发票科目的成本。 在每个产品订单内，成本按服务类型细分。 可以在 Azure 门户和 Azure 使用情况与费用 CSV 中找到产品和服务的每日费用。 若要了解详细信息，请参阅[了解 Microsoft 客户协议发票中的费用](review-customer-agreement-bill.md)。

每个服务系列的应付总金额在计算时，请从“额度/费用”中减去“Azure 额度”，然后添加“税款”：   


![按发票科目列出的详细信息](./media/mca-understand-your-invoice/invoicesectiondetails.png)

| 术语 |说明 |
| --- | --- |
| 单价 | 服务的有效单价（按定价货币衡量），用于按使用量计价。 这取决于产品、服务系列、计量和套餐。 |
| Qty | 在计费期间购买或消耗的量 |
| 费用/额度 | 在应用额度/退款后计得的净费用金额 |
| Azure 额度 | 应用到“费用/额度”的 Azure 额度的金额|
| 税率 | 税率取决于国家/地区 |
| 税额 | 根据税率应用到所购商品的税额 |
| 总计 | 所购商品的应付总金额 |

### <a name="how-to-pay"></a>如何支付

在发票底部有账单付款说明。 可以进行支票付款、电汇付款或在线付款。 如果进行在线付款，可以根据情况使用信用卡或 Azure 额度。

### <a name="publisher-information"></a>发布者信息

如果账单中有第三方服务，则每个发布者的名称和地址列在发票底部。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [了解计费对象信息的发票上的费用](review-customer-agreement-bill.md)
- [如何获取 Azure 计费发票和每日使用数据](../manage/download-azure-invoice-daily-usage-date.md)
- [查看组织的 Azure 定价](../manage/ea-pricing.md)
- [查看计费对象信息的税单](mca-download-tax-document.md)

---
title: 了解 Azure 中的 Microsoft 客户协议发票
description: 了解如何在 Azure 中阅读和了解你的 Microsoft 客户协议帐单
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: a5f77120c1d4e8a6721f3bc207132bee19a7772f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383537"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Microsoft 客户协议发票中的条款

本文适用于 Microsoft 客户协议的 Azure 计费帐户。 [检查你是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

发票提供费用的摘要和付款说明。 可以从 [Azure 门户](https://portal.azure.com/)下载可移植文档格式 (.pdf) 的发票，也可通过电子邮件获取。 有关详细信息, 请参阅[查看和下载 Microsoft Azure 发票](billing-download-azure-invoice.md)。

## <a name="billing-period"></a>计费周期

每月都按月开票。 您可以通过在[Azure 门户](https://portal.azure.com/)中的 "计费配置文件" 属性下检查*发票日期*来找出接收发票的月份日期。 在下个月的发票中, 计费周期结束时的费用将包含在下个月的发票中, 因为它们在下一个计费期间。 每张发票的计费周期的开始日期和结束日期将在发票 PDF 中的**发票上列出**。

## <a name="invoice-terms-and-descriptions"></a>发票条款和说明

以下部分列出了你在发票上看到的重要术语, 并提供每个术语的说明。

### <a name="invoice-summary"></a>发票摘要

**发票摘要**位于第一页的顶部, 并显示有关计费配置文件的信息以及支付方式。

![发票摘要部分](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| 术语 | 描述 |
| --- | --- |
| 买方 |你的法定实体的地址, 在计费帐户属性中|
| 帐单邮寄地址 |接收发票的计费配置文件的帐单地址, 在帐单配置文件属性中找到|
| 计费配置文件 |接收发票的计费配置文件的名称 |
| 邮箱 号 |可选采购订单号，由你分配用于跟踪 |
| 发票号 |用于跟踪的唯一的 Microsoft 生成发票号 |
| 发票日期 |生成发票的日期, 通常为计费周期结束后的5到12天。 您可以在帐单配置文件属性中检查您的发票日期。|
| 付款条款 |你为你的 Microsoft 帐单支付的费用。 *净30天*表示你在发票日期的30天内付款。 |

### <a name="billing-summary"></a>计费摘要

"**计费摘要**" 显示自上一个计费周期、应用的任何信用额度、税金和应付金额的计费配置文件的费用。

![计费摘要部分](./media/billing-understand-your-invoice-mca/billingsummary.png)

| 术语 | 描述 |
| --- | --- |
| 费用|自上次计费周期以来此计费配置文件的 Microsoft 费用总数 |
| 制作人员 |收到的信用额度返回 |
| 已应用 Azure 额度 | 自动应用于 Azure 的 azure 信用额度按计费周期计费 |
| 小计 |税前金额 |
| 税费 |根据计费配置文件的国家/地区支付的金额类型和金额。 如果不必支付税款, 则不会在发票上看到税款。 |
| 估计的总节省量 |从有效折扣中节省的估计总金额。 如果适用, 有效折扣率将在 "按发票详细信息" 部分的 "采购行项" 下列出。 |

### <a name="invoice-sections"></a>发票科目

对于帐单配置文件下的每个 "发票" 部分, 你将看到收费、应用的 Azure 信用额度、税款以及到期总额。

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>详细信息 (按发票部分)

详细信息显示按产品订单细分的每个发票部分的成本。 在每个产品订单中, 成本按服务类型细分。 你可以在 Azure 门户和 Azure 使用情况和费用 CSV 中找到你的产品和服务的每日费用。 若要了解详细信息, 请参阅[了解 Microsoft 客户协议对你的发票收取的费用](billing-mca-understand-your-bill.md)。

每个服务系列的总应付金额是通过将*Azure 信用*额度减去*信用额度/费用*并增加*税款*来计算的:


![详细信息 (按发票部分)](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| 术语 |描述 |
| --- | --- |
| 单位价格 | 服务的有效单价 (定价货币), 用于对使用量进行评级。 这对于产品、服务系列、计量和产品/服务是唯一的。 |
| Qty | 计费期间购买或使用的数量 |
| 费用/信用额度 | 应用信用/退款后的费用净额 |
| Azure 额度 | 应用于费用/信用额度的 Azure 信用额度|
| 税率 | 根据国家/地区的税率 |
| 税金 | 根据税率应用于采购的税金 |
| 总计 | 采购的总应付金额 |

### <a name="how-to-pay"></a>如何支付

发票底部有关于支付帐单的说明。 可以按支票、有线或联机付费。 如果你已联机付费, 则可以使用信用卡或 Azure 信用额度 (如果适用)。

### <a name="publisher-information"></a>发布者信息

如果在帐单中有第三方服务, 则每个出版商的名称和地址都列在发票底部。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助? 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [了解帐单配置文件的发票上的费用](billing-mca-understand-your-bill.md)
- [如何获取 Azure 计费发票和每日使用数据](billing-download-azure-invoice-daily-usage-date.md)
- [查看组织的 Azure 定价](billing-ea-pricing.md)
- [查看计费配置文件的税务文档](billing-mca-download-tax-document.md)

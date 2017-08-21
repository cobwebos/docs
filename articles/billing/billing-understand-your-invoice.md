---
title: "了解 Azure 发票 | Microsoft Docs"
description: "了解如何阅读并理解 Azure 订阅的使用情况和计费"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: tonguyen
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: a5be3c9bcdd99128217e441d62ce9650c9764580
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017


---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>了解 Microsoft Azure 发票的条款
该发票提供了收费摘要和有关付款的说明。 可以从 [Azure 门户](https://portal.azure.com/)下载可移植文档格式 (.pdf) 的发票，也可通过电子邮件获取。 有关详细信息，请参阅[如何获取 Azure 帐单发票和每日使用数据](billing-download-azure-invoice-daily-usage-date.md)。

需要注意的若干事项：

-   如果使用的是免费试用订阅，则可以从 Azure 门户获得每日使用情况明细信息，但没有发票。

-   在上一计费周期结束时，最多 24 小时的使用情况可能显示在当前发票中。

-   对于国际客户，帐单上列出的费用仅用于估算目的。 不同的银行可能会根据不同的兑换率计算费用。

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>发票的详细条款和说明
下列部分列出了发票上显示的重要条款以及每个条款的说明。

### <a name="account-information"></a>帐户信息

发票的帐户信息部分在第一页顶部，显示了配置文件和订阅的相关信息。

![发票的帐户信息部分](./media/billing-understand-your-invoice/1.png)

| 术语 | 说明 |
| --- | --- |
| 客户 PO 号 |可选采购订单号，由你分配用于跟踪 |
| 发票号 |由 Microsoft 生成的唯一发票号，用于跟踪 |
| 计费周期 |此发票涵盖的日期范围 |
| 发票日期 |生成发票的日期，通常是计费周期截止的后一天 |
| 付款方式 |在帐户中使用的付款类型（“发票”或“信用卡”） |
| 帐单邮寄地址 |列出的帐户的帐单地址 |
| 订阅产品/服务（“即用即付”） |购买的订阅产品/服务的类型（即用即付、BizSpark Plus、Azure Pass 等）。 有关详细信息，请参阅 [Azure 产品/服务类型](https://azure.microsoft.com/support/legal/offer-details/)。 |
| 帐户所有者电子邮件地址 | 注册 Microsoft Azure 帐户时使用的帐户电子邮件地址。 <br /><br />如何更改电子邮件地址，请参阅[如何更改联系人电子邮件、地址和电话号码等 Azure 帐户的个人资料信息](billing-how-to-change-azure-account-profile.md)。 |

### <a name="understand-the-invoice-summary"></a>了解发票汇总
发票的“发票摘要”部分汇总了上次计费周期之后的总交易量，以及当前的使用费。

![发票摘要部分](./media/billing-understand-your-invoice/2.png)

订阅名称（“生产存储”）为此发票订阅的名称。

#### <a name="understand-the-previous-charges"></a>了解过去的收费
发票的前期结余、付款和未付余额部分汇总了上次计费周期之后的交易。

| 术语 | 说明 |
| --- | --- |
| 前期结余 |上次计费周期中结余的总金额 |
| 付款 |应用到上次计费周期的总付款金额和信用额度 |
| 未付余额（从上一计费周期） |自上个计费周期起帐户中的任何信用额度或剩余余额 |

#### <a name="understand-the-current-charges"></a>了解当前费用
发票的“当前费用”部分显示有关当前计费周期中每月费用的详细信息。

| 术语 | 说明 |
| --- | --- |
| 使用费 |使用费是当前计费周期中，对订阅收取的每月总费用|
| 折扣 |应用到当前计费周期的服务折扣|
| 调整 |应用于当前计费周期的其他信用额度（免费使用、信用额度等）或未付费用。<br/><br/>例如，如果你享有 Visual Studio Enterprise with MSDN 优惠，你会看到每月信用额度。 如果取消订阅，你会看到超出通过订阅产品/服务获得的每月信用额度的所有每月使用费用。 从当前计费周期的开始计费，直到订阅取消日期。 |

#### <a name="sold-to-and-payment-instructions"></a>买方和付款说明

下表介绍发票第二页中显示的买方和付款说明。

| 术语 |说明 |
| --- | --- |
| 买方 |该帐户上的配置文件地址。 <br/><br/>如果需要更改地址，请参阅[如何更改联系人电子邮件、地址和电话号码等 Azure 帐户的个人资料信息](billing-how-to-change-azure-account-profile.md)。|
| 付款说明 |关于付款方式的说明（例如通过信用卡或通过发票付款）。 |

#### <a name="usage-charges"></a>使用费

发票的“使用费”部分介绍了关于收费的计量级别信息。

![使用费部分](./media/billing-understand-your-invoice/3.png)

下表介绍了显示在发票中的使用费列标题。

| 术语 |说明 |
| --- | --- |
| 名称 |标识该次使用的最上层服务 |
| 类型 |定义 Azure 服务类型，该类型可能会影响费率 |
| 资源 |标识已使用计量的度量单位 |
| 区域 |标识某些服务的数据中心的位置，这些服务根据数据中心位置进行定价 |
| 已耗用 |计费周期期间使用的计量的量 |
| 附送 |当前计费周期中免费附送的计量的量 |
| 可计费 |显示已耗用数量和已包含数量之间的差异。 将根据此数量对你计费。 对于不附送任何数量的即用即付产品/服务，此总数与已耗用数量相同 |
| 费率 |为每个可付费单位支付的费率 |
| 值 |显示将“超额数量”列与“费率”列相乘的结果。 如果已耗用数量未超过已包含数量，则此列中没有费用。 |
| 小计 |对此计费周期的税前收费总额 |
| 总计 |对此计费周期的税后收费总额 |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>如何确定发票中的费用是正确的？
如果想要更详细地了解发票上的费用，请参阅[了解你的 Microsoft Azure 帐单](billing-understand-your-bill.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。


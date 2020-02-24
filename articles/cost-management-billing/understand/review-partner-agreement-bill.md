---
title: 查看 Microsoft 合作伙伴协议发票 - Azure
description: 了解如何查看帐单和资源使用情况，以及如何验证 Microsoft 合作伙伴协议发票的费用。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: dd0d53fb89df5372d3974b85cdb659f6cfbbedd8
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199086"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>教程：查看 Microsoft 合作伙伴协议发票

 在 Microsoft 合作伙伴协议的计费帐户中，每月将为每个计费对象信息生成一份发票。 该发票包括上个月的所有客户费用。 可以通过分析 Azure 门户中的每笔交易来了解发票中的费用。 还可以在 Azure 门户中查看发票，并将费用与使用情况详细信息文件进行比较。

有关详细信息，请参阅[如何从 Azure 门户下载发票](download-azure-invoice.md)。

本教程仅适用于签订了 Microsoft 合作伙伴协议的 Azure 合作伙伴。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 门户中查看已开票交易
> * 查看未结费用以估算下一份发票
> * 分析 Azure 使用费

## <a name="prerequisites"></a>必备条件

你必须能够访问 Microsoft 合作伙伴协议的计费帐户。

在时间上必须超过 30 天，从订阅 Azure 那天算起。 在发票周期结束时，Azure 会向你开具账单。

## <a name="sign-in-to-azure"></a>登录 Azure

- 通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限

检查协议类型以确定你是否有权访问 Microsoft 合作伙伴协议的计费帐户。

在 Azure 门户中，在搜索框中键入“成本管理 + 计费”  ，然后选择“成本管理 + 计费”  。

![显示了 Azure 门户搜索的屏幕截图](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

如果仅有权访问一个计费范围，请从左侧选择“属性”  。 如果计费帐户类型为“Microsoft 合作伙伴协议”  ，则有权访问 Microsoft 合作伙伴协议的计费帐户。

![屏幕截图，在“属性”页中显示了 Microsoft 合作伙伴协议](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

如果有权访问多个计费范围，请检查“计费帐户”列中的类型。 如果任何范围的计费帐户类型为“Microsoft 合作伙伴协议”  ，则有权访问 Microsoft 合作伙伴协议的计费帐户。

![屏幕截图，在“计费帐户列表”页中显示了 Microsoft 合作伙伴协议](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>在 Azure 门户中查看已开票交易

在“成本管理 + 计费”中，从页面左侧选择“所有事务”。  根据你的访问权限，可能需要选择某个计费帐户、计费对象信息或客户，然后选择“所有交易”。 

“所有交易”页显示以下信息：

![显示计费交易列表的屏幕截图](./media/review-partner-agreement-bill/all-transactions.png)

|列  |定义  |
|---------|---------|
|Date     | 交易日期  |
|发票 ID     | 从中计收交易费的发票的标识符。 提交支持请求时，请与 Azure 支持人员共享该 ID，以加速支持请求的处理 |
|交易类型     |  交易类型，例如购买、取消和使用费  |
|产品系列     | 产品类别，例如，适用于虚拟机的计算产品，或适用于 Azure SQL 数据库的数据库产品|
|产品 SDKU     | 用于标识产品实例的唯一代码 |
|金额     |  交易额      |
|计费配置文件     | 交易显示在计费配置文件的发票中 |

搜索发票 ID 以筛选发票的交易。

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>查看未结费用以估算下一份发票

在开票之前，费用是预估的并视为待定。 可以在 Azure 门户中查看 Microsoft 合作伙伴协议计费对象信息的待定费用，以估计下一张发票。 未结费用是估算的，不包括税费。 下一份发票中的实际费用不同于未结费用。

### <a name="view-pending-transactions"></a>查看未结的交易

识别未结费用时，可以通过分析产生这些费用的每笔交易来了解费用。 此时，未结使用费不会显示在“所有交易”页上。 可以在 Azure 订阅页上查看未结使用费。

在“成本管理 + 计费”中，选择一个计费配置文件。 根据你的访问权限，可能需要选择一个计费帐户。 在该计费帐户中选择“计费配置文件”，然后选择一个计费配置文件。 

在页面左侧选择“所有事务”。 

搜索“未结”。  使用“时间跨度”筛选器查看当前月份或上一月份的未结费用。 

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>按客户查看待定费用

在“成本管理 + 计费”中，选择一个计费配置文件。 根据你的访问权限，可能需要选择一个计费帐户。 在该计费帐户中选择“计费配置文件”，然后选择一个计费配置文件。 

选择页面左侧的“客户”  。

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

“客户”页显示与计费对象信息关联的每个客户的当前费用和上个月费用。 本月至今累计费用是当前月份的未结费用，将在生成当月的发票时计收。 如果上个月的发票仍未生成，则上个月的费用也是未结的。

### <a name="view-pending-usage-charges"></a>查看未结使用费

在“成本管理 + 计费”中，选择一个计费配置文件。 根据你的访问权限，可能需要选择一个计费帐户。 在该计费帐户中选择“计费配置文件”，然后选择一个计费配置文件。 

在页面左侧选择“Azure 订阅”。  Azure 订阅页将显示计费配置文件中每个订阅的当月费用和上个月费用。 本月至今累计费用是当前月份的未结费用，将在生成当月的发票时计收。 如果上个月的发票仍未生成，则上个月的费用也是未结的。

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>分析 Azure 使用费

使用 Azure 使用情况和费用 CSV 文件来分析基于用量的费用。 可以筛选 Azure 使用情况和费用文件，以协调发票 pdf 中列出的每个产品的使用费用。 若要查看特定产品的详细使用费用，请筛选 Azure 使用情况和费用 CSV 文件中的“产品”  列，以仅包括该产品的名称。

还可以在 Azure 使用情况和费用 CSV 文件中筛选 **customerName** 列，以查看每个客户的每日使用费用。 如果要按 Azure 订阅查看每日使用费用，请筛选 **subscriptionName** 列。

## <a name="pay-your-bill"></a>支付帐单

有关支付帐单的说明显示在发票的底部。 可以在发票日期后 60 天内用电汇或支票付款。

如果已支付帐单，可以在 Azure 门户中的“发票”页上检查付款状态。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 门户中查看已开票交易
> * 查看未结费用以估算下一份发票
> * 分析 Azure 使用费

了解如何使用面向合作伙伴的 Azure 成本管理。

> [!div class="nextstepaction"]
> [面向合作伙伴的 Azure 成本管理入门](../costs/get-started-partners.md)

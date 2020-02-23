---
title: 来自 Azure 的逾期未付余款电子邮件
description: 说明在 Azure 订阅存在逾期未付余款的情况下如何进行支付
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: cbfd5f7bf47cdaf43df00c710bd6680373d67b09
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200599"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>处理 Azure 订阅的逾期未付余额问题

本文适用于使用信用卡注册了 Azure Online 并具有 Microsoft Online Services 计划计费帐户的客户。 了解如何[检查计费帐户类型](#check-the-type-of-your-account)。 如果你有 Microsoft 客户协议计费帐户，请参阅[支付 Microsoft Azure 的帐单](../understand/pay-bill.md)。

如果我们未收到你的付款或无法处理付款，你会收到一封电子邮件，并在 Azure 门户中看到一条警报，指出你的订阅逾期未付。 如果默认付款方式为信用卡，则[帐户管理员](billing-subscription-transfer.md#whoisaa)可以在 Azure 门户中结算未付费用。 如果付款方式为发票（支票/电汇），可将款项汇至发票底部列出的地点。

> [!IMPORTANT]
> * 如果你有多个订阅使用同一信用卡，并且这些订阅都已过期，则必须一次支付全部未付余额。
> * 对于使用失败付款方式的所有订阅，用于结算未付费用的信用卡将成为新的默认付款方式。

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>在 Azure 门户中处理逾期未付余额

1. 以帐户管理员身份登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 
1. 在“概述”页中选择逾期未付的订阅。 
1. 在“订阅概述”页中，单击红色的逾期未付横幅以结算余款。 
    > [!NOTE]
    > 如果你不是帐户管理员，则无法结算余款。
1. 在新的“结算余款”页中，单击“选择付款方式”。  
    ![显示“选择付款方式”链接的屏幕截图](./media/resolve-past-due-balance/settle-balance-screen.png)

1. 在右侧的新边栏选项卡中，从下拉列表中选择一张信用卡，或单击蓝色的“添加新付款方式”链接添加新卡。  对于当前使用失败付款方式的所有订阅，此信用卡将成为有效付款方式。
     > [!NOTE]
     > * 总未付余额反映了使用失败付款方式的所有 Microsoft 服务的未付费用。
     > * 如果所选的付款方式也有 Microsoft 服务的未付费用，则此费用将反映在总未付余额中。 你也必须支付这些未付费用。
1. 单击“付款”  。

## <a name="troubleshoot-declined-credit-card"></a>被拒信用卡故障排除

如果金融机构拒绝了你的信用卡收费，请与金融机构联系以解决此问题。 请向银行查询，以确保：
- 卡上启用了国际交易。
- 该卡有足够的额度限制或资金来结算余额。
- 卡上启用了定期付款功能。

## <a name="not-getting-billing-email-notifications"></a>收不到计费电子邮件通知？

如果你是帐户管理员，请[检查用于通知的电子邮件地址](change-azure-account-profile.md)。 我们建议使用定期收信的电子邮件地址。 如果电子邮件地址正确，请检查垃圾邮件文件夹。

## <a name="if-i-forget-to-pay-what-happens"></a>如果忘记付款，会发生什么？

服务会被取消，资源不再可用。 Azure 数据会在终止服务 90 天后删除。 若要了解详细信息，请参阅 [Microsoft Trust Center - How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)（Microsoft 信任中心 - 我们如何管理用户的数据）。

如果知道付款已被处理，但订阅仍处于禁用状态，请联系 [Azure 支持部门](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="check-the-type-of-your-account"></a>检查帐户的类型
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

---
title: 来自 Azure 的逾期未付余款电子邮件 | Microsoft 文档
description: 说明在 Azure 订阅存在逾期未付余款的情况下如何进行支付
services: ''
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: billing
ms.assetid: d0b88c92-fb9d-4d12-931b-c26104ad63e9
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: cd3c29c4b65f45863647599ae2447584845f27c8
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300286"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>处理 Azure 订阅的逾期未付余额问题

本文适用于使用 Microsoft 联机服务程序帐户的客户。

如果我们未收到付款或无法处理付款，你可能会收到一封电子邮件，或在 Azure 门户或帐户中心中看到一条警报。
如果你是[帐户管理员](billing-subscription-transfer.md#whoisaa)，可以在 [Azure 门户](https://portal.azure.com)中结算未付费用。 如果采用发票付款方式，可将付款寄送到发票底部列出的地点。

> [!IMPORTANT]
> * 如果你有多个订阅使用同一信用卡，并且这些订阅都已过期，则必须一次支付全部未付余额。
> * 对于使用失败付款方式的所有订阅，用于结算未付费用的付款方式将成为新的有效付款方式。

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>在 Azure 门户中处理逾期未付余额

1. 以帐户管理员身份登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”  。
1. 在“概述”页中，会看到订阅列表。 如果你的订阅状态为“逾期未付”，请单击“结算余额”  链接。
    ![显示“结算余额”链接的屏幕截图](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. 总未付余额反映了使用失败付款方式的所有 Microsoft 服务的未付费用。
1. 选择用于支付余额的付款方式。 对于当前使用失败付款方式的所有订阅，此付款方式将成为有效付款方式。
    ![显示“选择付款方式”链接的屏幕截图](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. 如果所选的付款方式也有 Microsoft 服务的未付费用，则此费用将反映在总未付余额中。 你也必须支付这些未付费用。
1. 单击“付款”  。

## <a name="troubleshoot-declined-credit-card"></a>被拒信用卡故障排除

如果金融机构拒绝了你的信用卡收费，请与金融机构联系以解决此问题。 请向银行查询，以确保：
- 卡上启用了国际交易。
- 该卡有足够的额度限制或资金来结算余额。
- 卡上启用了定期付款功能。

## <a name="not-getting-billing-email-notifications"></a>收不到计费电子邮件通知？

如果你是帐户管理员，请[检查用于通知的电子邮件地址](billing-how-to-change-azure-account-profile.md)。 我们建议使用定期收信的电子邮件地址。 如果电子邮件地址正确，请检查垃圾邮件文件夹。

## <a name="if-i-forget-to-pay-what-happens"></a>如果忘记付款，会发生什么？

服务会被取消，资源不再可用。 Azure 数据会在终止服务 90 天后删除。 若要了解详细信息，请参阅 [Microsoft Trust Center - How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)（Microsoft 信任中心 - 我们如何管理用户的数据）。

如果知道付款已被处理，但订阅仍处于禁用状态，请联系 [Azure 支持部门](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。


## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

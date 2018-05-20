---
title: 了解 Azure 支出限制 | Microsoft Docs
description: 介绍 Azure 支出限制的工作原理以及移除方式
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 6049e3614b63bfabee6721dcaa83008eb3306493
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>了解 Azure 支出限制以及移除方式

Azure 中的支出限制是为了防止支出超过信用额度。 如果注册使用试用版的新客户或具有几个月信用额度的产品/服务，将默认开启支出限制。 支出限制为 $0。 无法进行更改。 支出限制不可用于即用即付订阅和承诺计划等订阅类型。 请参阅 [Azure 产品/服务以及支出限制可用性的完整列表](https://azure.microsoft.com/support/legal/offer-details/)。

**想查找计费警报？** 请参阅[为 Azure 订阅设置计费或信用额度警报](billing-set-up-alerts.md)。

## <a name="what-happens-when-i-reach-the-spending-limit"></a>达到支出限制后会发生什么情况？

因使用而产生的费用已经用完了订阅中包括的每月额度时，部署的服务会在该计费月的剩余时间中被禁用。 

例如，用完订阅中所含信用额度后，部署的云服务会从生产中移除，Azure 虚拟机会被停止并取消分配。 你的存储帐户和数据库中的数据将只能通过只读方式访问。

在下一计费周期开始时，如果订阅产品/服务包含多个月的额度，则将自动重新启用订阅。 然后就可重新部署云服务，并可完全访问存储帐户和数据库。

达到订阅的支出限制时，我们会发送电子邮件通知。 登录到[帐户中心](https://account.windowsazure.com/Subscriptions)，会看到已达到支出限制的订阅的相关通知。

如果使用的是免费试用版并达到支出限制，可以[升级到即用即付](billing-upgrade-azure-subscription.md)以取消支出限制并自动重新启用订阅。

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>在帐户中心消除支出限制

只要订阅关联了有效的付款方法，即可随时移除支出限制。 对于具有几个月信用额度的产品/服务，可以在下个计费周期开始时重新启用支出限制。

若要移除支出限制，请按照以下步骤操作：

1. 登录到[帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 选择一个订阅。
。 如果订阅因达到支出限制而被禁用，请单击此通知：“订阅达到了支出限制，且已被禁用以免产生费用。” 否则，请单击“订阅状态”区域中的“移除支出限制”。
1. 选择适合选项。

![选择删除支出限制的选项](./media/billing-spending-limit/remove-spending-limit.PNG)

|选项|效果|
|-------|-----|
|永久性移除支出限制|移除支出限制，且在下次计费期开始不自动打开此限制。|
|移除当前计费期间的支出限制|移除支出限制，使其在下次计费期开始时自动重新打开。|

## <a name="frequently-asked-questions"></a>常见问题

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>为什么要移除支出限制？

支出限制会阻止你部署或使用某些第三方和 Microsoft 服务。 有些情况应移除订阅上的支出限制。

* 计划部署第一方映像（如 Oracle）和服务（如 Visual Studio Team Services）。 此方案几乎立马导致你超过支出限制并导致订阅被禁。
* 拥有不可中断的服务。
* 服务和资源包含希望保留的设置，如虚拟 IP 地址。 这些设置会在取消分配服务和资源时丢失。

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>取消支出限制后如何重新启用？

仅当已无限期移除支出限制时，此功能才可用。 将其更改为在下一计费周期开始时自动启用。

1. 登录到[帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 单击黄色横幅更改支出限制选项。
1. 选择“在下一计费周期的\<计费周期的开始日期\>启用支出限制”

### <a name="how-do-i-set-a-custom-spending-limit"></a>如何设置自定义支出限制？

目前无法设置。 但可以选择[使用计费警报控制支出](billing-set-up-alerts.md)。

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>支出限制是否可防止产生任何 Azure 费用？

[已在 Azure Marketplace 中发布的一些外部服务](billing-understand-your-azure-marketplace-charges.md)不能与订阅信用额度一起使用，即使设置了支出限制，这些服务也会产生单独的费用。 例如，Visual Studio 许可证、Azure Active Directory Premium、支持计划以及大多数第三方品牌服务。 预配新的外部服务时，将显示一条提示此服务会单独计费的警告：

![Marketplace 购买警告](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

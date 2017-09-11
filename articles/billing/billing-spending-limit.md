---
title: "了解 Azure 支出限制 | Microsoft Docs"
description: "介绍 Azure 支出限制的工作原理以及移除方式"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: a2743ef34bde0faabb3afd2ace27acddd59d3d70
ms.contentlocale: zh-cn
ms.lasthandoff: 08/09/2017

---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>了解 Azure 支出限制以及移除方式

Azure 支出限制用于限制 Azure 订阅支出额。 如果新客户注册包括几个月信用额度的试用版产品/服务，则这些客户都将默认开启支出限制。 支出限制为 $0。 无法进行更改。 支出限制不可用于即用即付订阅和承诺计划等订阅类型。 请参阅 [Azure 产品/服务以及支出限制可用性的完整列表](https://azure.microsoft.com/support/legal/offer-details/)。

## <a name="what-happens-when-i-reach-the-spending-limit"></a>达到支出限制后会发生什么情况？

因使用而产生的费用已经用完了产品/服务中包括的每月额度时，部署的服务会在该计费月的剩余时间中被禁用。 例如，部署的云服务会从生产中移除，Azure 虚拟机会被停止并取消分配。 要阻止服务被禁用，可以选择删除支出限制。 当服务被禁用时，只能以只读模式向管理员提供存储帐户和数据库中的数据。 在下一计费月开始时，如果产品/服务包含多个月的额度，则将重新启用订阅。 然后就可重新部署云服务，并可完全访问存储帐户和数据库。

在免费试用版订阅达到支出限制后，可重新启用订阅，并使其在 90 天内自动[升级到标准的即用即付产品/服务](billing-upgrade-azure-subscription.md)。

点击产品/服务的支出限制时，即会收到通知。 登录 [Azure 帐户中心](https://account.windowsazure.com)，选择“帐户”，并选择“订阅”。 会看到已达到支出限制的订阅的相关通知。

## <a name="things-you-are-charged-for-even-if-you-have-a-spending-limit-enabled"></a>即使启用了支出限制也会收取费用的事项

即使设定了支出限制，某些 Azure 服务和 [Marketplace 购买项](https://azure.microsoft.com/marketplace/)也会在付款方式 (CC) 下产生费用。 例如，Visual Studio 许可证、Azure Active Directory Premium、支持计划以及通过 Marketplace 销售的大多数第三方品牌服务。


## <a name="when-not-to-use-the-spending-limit"></a>不使用支出限制的情况

支出限制会阻止你部署或使用某些 Marketplace 和 Microsoft 服务。 有些情况应移除订阅上的支出限制。

- 计划部署第一方映像（如 Oracle）和服务（如 Visual Studio Team Services）。 此方案几乎立马导致你超过支出限制并导致订阅被禁。

- 拥有不可中断的服务。

- 服务和资源包含希望保留的设置，如虚拟 IP 地址。 这些设置会在取消分配服务和资源时丢失。


## <a name="remove-the-spending-limit"></a>移除支出限制

只要订阅关联了有效的付款方法，即可随时移除支出限制。 对于具有几个月信用额度的产品/服务，可以在下个计费周期开始时重新启用支出限制。

若要移除支出限制，请按照以下步骤操作：

1. 登录到 [Azure 帐户中心](https://account.windowsazure.com)。

2. 选择一个订阅。

3. 如果订阅因达到支出限制而被禁用，请单击此通知：“订阅达到了支出限制，且已被禁用以免产生费用”。 否则，请单击“订阅状态”区域中的“移除支出限制”。

4. 选择适合选项。

|选项|效果|
|-------|-----|
|永久性移除支出限制|移除支出限制，且在下次计费期开始不自动打开此限制。|
|移除当前计费期间的支出限制|移除支出限制，使其在下次计费期开始时自动重新打开。|

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。


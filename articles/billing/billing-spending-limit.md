---
title: Azure 支出限制 |Microsoft Docs
description: 本文介绍 Azure 支出限制的工作原理以及如何将其删除。
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fb43f29827309fc8986ee6b4653f5edf303cc21d
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490615"
---
# <a name="azure-spending-limit"></a>Azure 支出限制

在 Azure 中的支出限制可以防止支出超过信用额度。 注册 Azure 试用版或包含几个月信用额度的产品/服务的所有新客户已默认开启支出限制。 支出限制为 $0。 无法进行更改。 在某些订阅计划此类承诺计划和使用即用即付定价计划的支出限制不可用。 请参阅 [Azure 产品/服务以及支出限制可用性的完整列表](https://azure.microsoft.com/support/legal/offer-details/)。

## <a name="reaching-a-spending-limit"></a>达到支出限制

使用而产生的费用已经用完了按月计费模式包括时与 Azure 订阅，你部署的服务被禁用的该计费期间剩余部分。

例如，订阅中包含的所有信用额度后，从生产中移除你部署的 Azure 资源和 Azure 虚拟机被停止并取消分配。 你的存储帐户中的数据都可用作只读的。

在下一计费周期开始时，如果订阅产品/服务包括多个月信用额度是重新启用订阅自动。 然后可以重新部署 Azure 资源，并具有对存储帐户和数据库的完全访问权限。

Azure 将发送电子邮件通知时达到支出限制为你的订阅。 登录到[帐户中心](https://account.windowsazure.com/Subscriptions)若要查看有关订阅已达到支出限制的通知。

如果具有免费的试用版订阅达到支出限制，你可以升级到与计划[即用即付](billing-upgrade-azure-subscription.md)定价删除支出限制，并自动启用订阅。

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>在帐户中心消除支出限制

只要与你的 Azure 订阅关联的有效的付款方法，可以删除支出限制在任何时间。 对于具有几个月的信用额度的服务，你可以在下一个帐期开始时启用支出限制。

若要移除支出限制，请按照以下步骤操作：

1. 登录到[帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 选择一个订阅。 如果订阅因达到支出限制而被禁用，则单击该通知：**订阅已达到支出限制并已被禁用以免产生费用。** 否则，请单击“订阅状态”区域  中的“移除支出限制”  。
1. 选择适合选项。

![选择删除支出限制的选项](./media/billing-spending-limit/remove-spending-limit.PNG)

| Option | 效果 |
| --- | --- |
| 永久性移除支出限制 | 移除支出限制，且在下次计费期开始不自动打开此限制。 |
| 移除当前计费期间的支出限制 | 移除支出限制，使其在下次计费期开始时自动重新打开。 |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>为什么你可能想要移除支出限制

支出限制会阻止你部署或使用某些第三方和 Microsoft 服务。 以下是应在你的订阅移除支出限制的情况。

-  计划部署第一方映像（如 Oracle）和服务（如 Azure DevOps Services）。 这种情况下会使你几乎可以立即超出支出限制并导致订阅被禁用。
- 您有不希望中断的服务。
- 服务和资源包含希望保留的设置，如虚拟 IP 地址。 这些设置会在取消分配服务和资源时丢失。

## <a name="turn-on-the-spending-limit-after-removing"></a>开启支出限制后删除

仅当已无限期移除支出限制时，此功能才可用。 将其更改为在下一计费周期开始时自动启用。

1. 登录到[帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 单击黄色横幅更改支出限制选项。
1. 选择“在下一计费周期的\<计费周期的开始日期\>启用支出限制” 

## <a name="custom-spending-limit"></a>自定义支出限制

自定义支出限制不可用。

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>支出限制不会阻止所有费用

[Azure Marketplace 中发布一些外部服务](billing-understand-your-azure-marketplace-charges.md)不能用于你订阅的信用额度，并可以产生单独的费用，即使你的支出限制设置。 例如，Visual Studio 许可证、Azure Active Directory Premium、支持计划以及大多数第三方品牌服务。 预配新的外部服务时，将显示一条提示此服务会单独计费的警告：

![市场购买警告](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 升级到具有的计划[即用即付](billing-upgrade-azure-subscription.md)定价。

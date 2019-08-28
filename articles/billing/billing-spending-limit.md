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
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 1324b60de05805cd409975358ff78e5b2b27eef1
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114453"
---
# <a name="azure-spending-limit"></a>Azure 支出限制

Azure 中的支出限制禁止支出超过你的额度。 注册 Azure 试用版或包含多个月信用额度的产品/服务的所有新客户默认开启支出限制。 支出限制为 $0, 无法更改。 例如, 不能将支出限制更改为 $100。 不过, 您可以移除支出限制。 因此, 您无限制, 或者您的限制为零, 这会阻止大多数类型的支出。 使用即用即付定价, 某些计划下的订阅不支持支出限制。 请参阅 [Azure 产品/服务以及支出限制可用性的完整列表](https://azure.microsoft.com/support/legal/offer-details/)。

## <a name="reaching-a-spending-limit"></a>达到支出限制

当你的使用费用导致 Azure 订阅中包含的月度金额用尽时, 你部署的服务将在该计费周期的剩余时间内禁用。

例如, 如果你在订阅中包含的所有信用额度, 则会从生产中删除你部署的 Azure 资源, 并且你的 Azure 虚拟机将停止并取消分配。 存储帐户中的数据以只读形式提供。

在下一个计费周期开始时, 如果订阅产品/服务包含多个月的信用额度, 则将自动重新启用订阅。 然后, 你可以重新部署 Azure 资源, 并对存储帐户和数据库具有完全访问权限。

当你达到订阅的支出限制时, Azure 会发送电子邮件通知。 登录到[帐户中心](https://account.windowsazure.com/Subscriptions), 查看有关已达到支出限制的订阅的通知。

如果你有免费试用版订阅并达到支出限制, 则可以使用现用现[付](billing-upgrade-azure-subscription.md)定价升级到计划, 以消除支出限制并自动启用订阅。

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>在帐户中心消除支出限制

只要存在与你的 Azure 订阅关联的有效付款方式, 你就可以随时删除支出限制。 对于有多个月信用额度的产品/服务, 还可以在下一个计费周期开始时启用支出限制。

若要移除支出限制，请按照以下步骤操作：

1. 登录到[帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 选择一个订阅。 如果订阅因达到支出限制而被禁用, 请单击通知:**订阅已达到支出限制, 已被禁用以防止收费。** 否则，请单击“订阅状态”区域中的“移除支出限制”。
1. 选择适合选项。

![选择删除支出限制的选项](./media/billing-spending-limit/remove-spending-limit.PNG)

| 选项 | 效果 |
| --- | --- |
| 无限期移除支持限制 | 移除支出限制，且在下次计费期开始不自动打开此限制。 |
| 移除当前计费期间的支出限制 | 移除支出限制，使其在下次计费期开始时自动重新打开。 |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>为什么要删除支出限制

支出限制会阻止你部署或使用某些第三方和 Microsoft 服务。 在这种情况下, 应删除订阅的支出限制。

-  计划部署第一方映像（如 Oracle）和服务（如 Azure DevOps Services）。 这种情况会导致你几乎立即超出支出限制, 并会导致订阅被禁用。
- 你有不希望中断的服务。
- 服务和资源包含希望保留的设置，如虚拟 IP 地址。 这些设置会在取消分配服务和资源时丢失。

## <a name="turn-on-the-spending-limit-after-removing"></a>启用删除后的支出限制

仅当已无限期移除支出限制时，此功能才可用。 将其更改为在下一计费周期开始时自动启用。

1. 登录到[帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 单击黄色横幅更改支出限制选项。
1. 选择“在下一计费周期的\<计费周期的开始日期\>启用支出限制”

## <a name="custom-spending-limit"></a>自定义支出限制

自定义支出限制不可用。

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>支出限制不会阻止所有费用

[Azure Marketplace 中发布的某些外部服务](billing-understand-your-azure-marketplace-charges.md)不能与订阅信用额度一起使用, 并且即使设置了支出限制, 也可能会产生单独的费用。 例如，Visual Studio 许可证、Azure Active Directory Premium、支持计划以及大多数第三方品牌服务。 预配新的外部服务时，将显示一条提示此服务会单独计费的警告：

![市场购买警告](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果你有疑问或需要帮助, 请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 使用即用[即付](billing-upgrade-azure-subscription.md)定价升级到计划。

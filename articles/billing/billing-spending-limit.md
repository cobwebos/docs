---
title: Azure 支出限制 | Microsoft Docs
description: 本文介绍 Azure 支出限制的工作原理以及移除方式。
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70114453"
---
# <a name="azure-spending-limit"></a>Azure 支出限制

Azure 中的支出限制可以防止支出超过额度金额。 如果新客户注册包括几个月额度的 Azure 试用版或套餐，则这些客户都将默认开启支出限制。 支出限制为 $0，不能更改。 例如，不能将支出限制更改 $100。 但是，可以移除支出限制。 因此，你要么没有限制，要么限制为零，这样你就无法完成大多数类型的支出。 支出限制不适用于某些计划中的订阅，例如承诺计划以及采用即用即付定价的计划。 请参阅 [Azure 产品/服务以及支出限制可用性的完整列表](https://azure.microsoft.com/support/legal/offer-details/)。

## <a name="reaching-a-spending-limit"></a>达到支出限制

因使用而产生的费用已经用完了 Azure 订阅中包括的每月额度时，部署的服务会在该计费月的剩余时间中被禁用。

例如，用完订阅中所含额度后，部署的 Azure 资源会从生产中移除，Azure 虚拟机会被停止并取消分配。 你的存储帐户中的数据将只能通过只读方式访问。

在下一计费周期开始时，如果订阅套餐包含多个月的额度，则会自动重新启用订阅。 然后就可重新部署 Azure 资源，并可完全访问存储帐户和数据库。

达到订阅的支出限制时，Azure 会发送电子邮件通知。 登录到[帐户中心](https://account.windowsazure.com/Subscriptions)，会看到已达到支出限制的订阅的相关通知。

如果使用的是免费试用版订阅并达到支出限制，可以升级到采用[即用即付](billing-upgrade-azure-subscription.md)定价的计划，以取消支出限制并自动启用订阅。

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>在帐户中心消除支出限制

只要 Azure 订阅关联了有效的付款方法，即可随时移除支出限制。 对于具有几个月额度的套餐，可以在下个计费周期开始时启用支出限制。

若要移除支出限制，请按照以下步骤操作：

1. 登录到[帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 选择一个订阅。 如果订阅因达到支出限制而被禁用，请单击此通知：**订阅已达到支出限制并已被禁用以免产生费用。** 否则，请单击“订阅状态”区域  中的“移除支出限制”  。
1. 选择适合选项。

![选择删除支出限制的选项](./media/billing-spending-limit/remove-spending-limit.PNG)

| 选项 | 效果 |
| --- | --- |
| 永久性移除支出限制 | 移除支出限制，且在下次计费期开始不自动打开此限制。 |
| 移除当前计费期间的支出限制 | 移除支出限制，使其在下次计费期开始时自动重新打开。 |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>为什么需要移除支出限制

支出限制会阻止你部署或使用某些第三方和 Microsoft 服务。 有些情况应移除订阅上的支出限制。

-  计划部署第一方映像（如 Oracle）和服务（如 Azure DevOps Services）。 此情况几乎立马导致你超过支出限制并导致订阅被禁。
- 拥有不希望其中断的服务。
- 服务和资源包含希望保留的设置，如虚拟 IP 地址。 这些设置会在取消分配服务和资源时丢失。

## <a name="turn-on-the-spending-limit-after-removing"></a>取消支出限制后重新启用该限制

仅当已无限期移除支出限制时，此功能才可用。 将其更改为在下一计费周期开始时自动启用。

1. 登录到[帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 单击黄色横幅更改支出限制选项。
1. 选择“在下一计费周期的\<计费周期的开始日期\>启用支出限制” 

## <a name="custom-spending-limit"></a>自定义支出限制

自定义支出限制不可用。

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>支出限制并不防止所有费用

[已在 Azure 市场中发布的一些外部服务](billing-understand-your-azure-marketplace-charges.md)不能与订阅额度一起使用，即使设置了支出限制，这些服务也会产生单独的费用。 例如，Visual Studio 许可证、Azure Active Directory Premium、支持计划以及大多数第三方品牌服务。 预配新的外部服务时，将显示一条提示此服务会单独计费的警告：

![市场购买警告](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 升级到采用[即用即付](billing-upgrade-azure-subscription.md)定价的计划。

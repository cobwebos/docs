---
title: Azure 支出限制 | Microsoft Docs
description: 本文介绍 Azure 支出限制的工作原理以及移除方式。
author: bandersmsft
manager: judupont
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: bde3c142fa0f4f69948a9ff1df61d77f06d2b430
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188285"
---
# <a name="azure-spending-limit"></a>Azure 支出限制

Azure 中的支出限制可以防止支出超过额度金额。 如果新客户注册包括几个月额度的 Azure 免费帐户或订阅类型，则这些客户都将默认开启支出限制。 支出限制为额度金额，不能更改。 例如，如果注册获取 Azure 免费帐户，则支出限制为 200 美元，不能将其更改为 500 美元。 但是，可以移除支出限制。 因此，你要么没有限制，要么限制为额度金额。 这导致你无法完成大多数类型的支出。 支出限制不适用于具有承诺计划或使用即用即付定价的订阅。 请参阅 [Azure 订阅类型以及支出限制可用性的完整列表](https://azure.microsoft.com/support/legal/offer-details/)。

## <a name="reaching-a-spending-limit"></a>达到支出限制

因使用而产生的费用已经用完了支出限制时，部署的服务会在该计费月的剩余时间中被禁用。

例如，用完 Azure 免费帐户中所含的额度后，部署的 Azure 资源会从生产中移除，Azure 虚拟机会被停止并取消分配。 你的存储帐户中的数据将只能通过只读方式访问。

在下一计费周期开始时，如果订阅类型包含多个月的额度，则会自动重新启用订阅。 然后就可重新部署 Azure 资源，并可完全访问存储帐户和数据库。

达到支出限制时，Azure 会发送电子邮件通知。 登录到 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，查看已达到支出限制的订阅的相关通知。

如果注册获取 Azure 免费帐户并达到支出限制，可以升级到[即用即付](upgrade-azure-subscription.md)定价，以取消支出限制并自动重新启用订阅。

## <a name="remove-the-spending-limit-in-azure-portal"></a>在 Azure 门户中消除支出限制

只要 Azure 订阅关联了有效的付款方法，即可随时移除支出限制。 对于具有多个月额度的订阅类型（例如 Visual Studio Enterprise 和 Visual Studio Professional），你可以选择无限期移除支出限制，也可以只移除当前计费周期的支出限制。 如果你只选择当前计费周期，则在下一个计费周期开始时将自动启用支出限制。

如果你有 Azure 免费帐户，请参阅[升级 Azure 订阅](upgrade-azure-subscription.md)来移除支出限制。 否则，请按照以下步骤来移除支出限制：

<a id="remove"></a>

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 

    ![显示搜索“成本管理 + 计费”的屏幕截图 ](./media/spending-limit/search-bar.png)

1. 在“我的订阅”  列表中，选择你的订阅。 例如 *Visual Studio Enterprise*。 

   ![显示我的订阅网格概览的屏幕截图](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > 如果在此处未看到你的某些 Visual Studio 订阅，则可能是因为你在某个时间点更改了订阅目录。 对于这些订阅，你需要将目录切换回原始目录（你最初在其中进行注册的目录）。 然后，重复步骤 2。
    
1. 在订阅概览中，单击橙色横幅删除支出限制。
    
    ![显示删除支出限制横幅的屏幕截图](./media/spending-limit/msdn-remove-spending-limit-banner-x.png)

1. 选择是要永久删除支出限制还是仅为当前计费周期删除。
    
      ![显示删除支出限制边栏选项卡的屏幕截图](./media/spending-limit/remove-spending-limit-blade-x.png)
    
      | 选项 | 效果 |
      | --- | --- |
      | 永久性移除支出限制 | 支出限制在下个计费周期开始时不会自动重新启用。 但是，你随时可以将其重新启用。 |
      | 移除当前计费期间的支出限制 | 支出限制在下个计费周期开始时会自动重新启用。 |


1. 单击“选择付款方式”，为订阅选择一个付款方式  。 这将成为订阅的有效付款方式。

1. 单击“完成”  。


## <a name="why-you-might-want-to-remove-the-spending-limit"></a>为什么需要移除支出限制

支出限制会阻止你部署或使用某些第三方和 Microsoft 服务。 有些情况应移除订阅上的支出限制。

-  你计划部署第三方映像（如 Oracle）或服务（如 Azure DevOps Services）。 此情况几乎立马导致你达到支出限制并导致订阅被禁。
- 拥有不希望其中断的服务。 达到支出限制时，会从生产中移除部署的 Azure 资源，Azure 虚拟机会被停止并取消分配。 如果有不希望其中断的服务，则必须消除支出限制。
- 服务和资源包含希望保留的设置，如虚拟 IP 地址。 这些设置会在达到支出限制且服务和资源被取消分配时丢失。

## <a name="turn-on-the-spending-limit-after-removing"></a>取消支出限制后重新启用该限制

对于包含多个月额度的订阅类型来说，仅当已无限期移除支出限制时，此功能才可用。 可以使用此功能，在下次计费期开始时自动打开支出限制。


1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 

    ![显示搜索“成本管理 + 计费”的屏幕截图 ](./media/spending-limit/search-bar.png)

1. 在“我的订阅”  列表中，选择你的订阅。 例如 *Visual Studio Enterprise*。 

   ![显示我的订阅网格概览的屏幕截图](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > 如果在此处未看到你的某些 Visual Studio 订阅，则可能是因为你在某个时间点更改了订阅目录。 对于这些订阅，你需要将目录切换回原始目录（你最初在其中进行注册的目录）。 然后，重复步骤 2。
    
1. 在订阅概述中，单击页面顶部的横幅以重新启用支出限制。

## <a name="custom-spending-limit"></a>自定义支出限制

自定义支出限制不可用。

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>支出限制并不防止所有费用

[已在 Azure 市场中发布的一些外部服务](../understand/understand-azure-marketplace-charges.md)不能与订阅额度一起使用，即使设置了支出限制，这些服务也会产生单独的费用。 例如，Visual Studio 许可证、Azure Active Directory Premium、支持计划以及大多数第三方品牌服务。 预配新的外部服务时，将显示一条提示此服务会单独计费的警告：

![市场购买警告](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 升级到采用[即用即付](upgrade-azure-subscription.md)定价的计划。

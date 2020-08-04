---
title: 取消 Azure 订阅
description: 介绍如何取消 Azure 订阅，例如免费试用版订阅
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: banders
ms.openlocfilehash: 8b0ba7b597921d82883f2c7ba505ebb10eee1876
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87039761"
---
# <a name="cancel-your-azure-subscription"></a>取消 Azure 订阅

如果不再需要订阅，可以在 Azure 门户中取消 Azure 订阅。

Microsoft 建议你在取消订阅之前执行以下操作，虽然这不是必需的：

* 备份数据。 例如，若要在 Azure 存储或 SQL 中存储数据，请下载一个副本。 如果有虚拟机，请在本地保存该虚拟机的映像。
* 关闭服务。 转到[管理门户中的资源页](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)，**停止**任何正在运行的虚拟机、应用程序或其他服务。
* 考虑迁移数据。 请参阅[将资源移到新资源组或订阅中](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。
* 删除所有资源和所有资源组。
* 如果在 `AssignableScopes` 中有任何引用此订阅的自定义角色，则应更新这些自定义角色以删除此订阅。 如果在取消订阅后尝试更新自定义角色，可能会收到错误。 有关详细信息，请参阅[排查自定义角色问题](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles)和 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)。

如果取消付费的 Azure 支持计划，则需为剩余的订阅期付费。 有关详细信息，请参阅 [Azure 支持计划](https://azure.microsoft.com/support/plans/)。

## <a name="cancel-subscription-in-the-azure-portal"></a>在 Azure 门户中取消订阅

1. 从 [Azure 门户中的“订阅”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)选择订阅。
2. 选择要取消的订阅。
3. 选择“概述”，然后选择“取消订阅” 。
    ![显示“取消”按钮的屏幕截图](./media/cancel-azure-subscription/cancel_ibiza.png)
3. 按照提示进行操作并完成取消。

## <a name="who-can-cancel-a-subscription"></a>谁可以取消订阅？

下表说明了取消订阅所需的权限。

|订阅类型     |谁可以取消  |
|---------|---------|
|通过 Azure 网站注册 Azure 时创建的订阅。 例如，当你注册 [Azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)、[采用即用即付费率的帐户](https://azure.microsoft.com/offers/ms-azr-0003p/)或作为 [Visual Studio 订阅者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)时。 |  订阅的帐户管理员和所有者  |
|[Microsoft 企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)和 [Enterprise 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  订阅的帐户所有者和所有者       |
|[Azure 计划](https://azure.microsoft.com/offers/ms-azr-0017g/)和 [Azure 开发测试计划](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  订阅的所有者      |

## <a name="what-happens-after-i-cancel-my-subscription"></a>取消订阅之后会发生什么情况？

取消后，会立即停止计费。 但是，可能需要花费多达 10 分钟，取消才会显示在门户中。 如果在计费周期中途取消订阅，我们会在周期结束后于标准发票日期发送最终账单。 

取消后，你的服务将被禁用。 那意味着虚拟机已解除分配，临时 IP 地址已释放，并且存储是只读的。

Microsoft 会先等待 30 - 90 天，然后才会永久删除用户的数据，这样做是考虑到用户可能需要访问这些数据，或者改变了主意。 我们不会因为保留这些数据而向你收费。 若要了解详细信息，请参阅 [Microsoft Trust Center - How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)（Microsoft 信任中心 - 我们如何管理用户的数据）。

## <a name="reactivate-subscription"></a>重新激活订阅

如果意外取消了采用即用即付费率的订阅，可[在帐户中心重新激活订阅](subscription-disabled.md)。

如果你的订阅不是采用即用即付费率的订阅，可在取消后的 90 天内联系支持人员来重新激活订阅。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- 如果需要，可以在[帐户中心](subscription-disabled.md)重新激活即用即付订阅。
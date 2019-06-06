---
title: 取消 Azure 订阅 | Microsoft Docs
description: 介绍如何取消 Azure 订阅，例如免费试用版订阅
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 8f4279d9ac085cdd1ded0dfdda4fad9d3fe12fb8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480236"
---
# <a name="cancel-your-subscription-for-azure"></a>取消针对 Azure 的订阅

只有 Azure 订阅[帐户管理员](billing-subscription-transfer.md#whoisaa)可以取消 Azure 订阅。 Azure 订阅管理员还可以[指定的订阅管理员的另一用户](billing-add-change-azure-subscription-administrator.md#assign-a-user-as-an-administrator-of-a-subscription)，以便它们可以取消订阅。 取消订阅后，将无法访问 Azure 服务和资源。

在取消订阅之前，请执行以下操作：

* 备份数据。 例如，若要在 Azure 存储或 SQL 中存储数据，请下载一个副本。 如果有虚拟机，请在本地保存该虚拟机的映像。
* 关闭服务。 转到[管理门户中的资源页](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)，**停止**任何正在运行的虚拟机、应用程序或其他服务。
* 考虑迁移数据。 请参阅[将资源移到新资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。
* 必须删除所有资源和所有资源组。 可以取消订阅之前删除它们是必需的。 必须单独删除每个资源组。 在删除资源组，必须通过键入资源组名称来确认删除。
* 如果有任何引用此订阅中的自定义角色`AssignableScopes`，应更新这些自定义角色，以删除订阅。 如果你尝试更新自定义角色后取消订阅，可能会遇到错误。 有关详细信息，请参阅[自定义角色的疑难解答](../role-based-access-control/troubleshooting.md#problems-with-custom-roles)并[Azure 资源的自定义角色](../role-based-access-control/custom-roles.md)。

如果取消付费的 Azure 支持计划，则仍需为剩余的订阅期付费。 有关详细信息，请参阅 [Azure 支持计划](https://azure.microsoft.com/support/plans/)。

## <a name="cancel-subscription-using-the-azure-portal"></a>使用 Azure 门户取消订阅

1. 从 [Azure 门户中的“订阅”页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择订阅。
2. 选择要取消的订阅。
3. 选择“概述”，然后选择“取消订阅”   。

    ![显示“取消”按钮的屏幕截图](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. 按照提示进行操作并完成取消。

## <a name="what-happens-after-i-cancel-my-subscription"></a>取消订阅之后会发生什么情况？

取消后，会立即停止计费。 但是，可能需要花费多达 10 分钟，取消才会显示在门户中。 如果在计费周期中途取消订阅，我们会在周期结束后于标准发票日期发送最终帐单。

取消后，服务被禁用。 那意味着虚拟机已解除分配，临时 IP 地址已释放，并且存储是只读的。

我们会先等待 90 天，才会永久删除用户的数据，这样做是考虑到用户可能需要访问这些数据，或者改变了主意。 我们不会因为保留这些数据而向你收费。 若要了解详细信息，请参阅 [Microsoft Trust Center - How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)（Microsoft 信任中心 - 我们如何管理用户的数据）。

## <a name="reactivate-subscription"></a>重新激活订阅

如果意外取消了即用即付订阅，可[在帐户中心重新激活订阅](billing-subscription-become-disable.md)。

如果你的订阅不是即用即付订阅，可在取消后的 90 天内联系支持人员来重新激活你的订阅。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

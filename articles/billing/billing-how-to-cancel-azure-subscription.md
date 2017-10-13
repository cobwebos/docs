---
title: "取消 Azure 订阅 | Microsoft Docs"
description: "介绍如何取消 Azure 订阅，例如免费试用版订阅"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 51fe2ab891e86ae4bd7402622231af47f35aa01d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="cancel-your-subscription-for-azure"></a>取消针对 Azure 的订阅

可以以[帐户管理员身份](billing-subscription-transfer.md#whoisaa)取消 Azure 订阅。 取消订阅后，将无法访问 Azure 服务和资源。

在取消订阅之前，请执行以下操作：

* 备份数据。 例如，若要在 Azure 存储或 SQL 中存储数据，请下载一个副本。 如果有虚拟机，请在本地保存该虚拟机的映像。
* 关闭服务。 转到[管理门户中的资源页](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)，**停止**任何正在运行的虚拟机、应用程序或其他服务。
* 考虑迁移数据。 请参阅[将资源移到新资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。

取消付费的 [Azure 支持计划](https://azure.microsoft.com/support/plans/)后，仍会按月向用户收取余下 6 个月的费用。

## <a name="cancel-subscription-using-the-azure-portal"></a>使用 Azure 门户取消订阅

1. 从 [Azure 门户中的“订阅”页面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择订阅。
1. 选择要取消的订阅，然后单击“取消订阅”。

    ![显示“取消”按钮的屏幕截图](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. 按照提示进行操作并完成取消。

## <a name="what-happens-after-i-cancel-my-subscription"></a>取消订阅之后会发生什么情况？

在取消后，计费将立即停止。 但是，可能需要花费多达 10 分钟，取消才会显示在门户中。

在那之后，服务被禁用。 那意味着虚拟机已解除分配，临时 IP 地址已释放，并且存储是只读的。

如果在计费周期中途取消订阅，我们会在周期结束后于标准发票日期发送最终帐单。 

我们会先等待 90 天，才会永久删除用户的数据，这样做是考虑到用户可能需要访问这些数据，或者改变了主意。 我们不会因为保留这些数据而向你收费。 若要了解详细信息，请参阅 [Microsoft Trust Center - How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)（Microsoft 信任中心 - 我们如何管理用户的数据）。

## <a name="reactivate-subscription"></a>重新激活订阅

如果意外取消了即用即付订阅，可[在帐户中心重新激活订阅](billing-subscription-become-disable.md)。

如果你的订阅不是即用即付订阅，可在取消后的 90 天内联系支持人员来重新激活你的订阅。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

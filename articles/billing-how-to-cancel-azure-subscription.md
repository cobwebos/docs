---
title: "取消 Azure 订阅 | Microsoft Docs"
description: "介绍如何取消 Azure 订阅，例如免费试用版订阅"
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/6/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: c23c92234b38eeb911bd316fffe5127bbad4de91
ms.openlocfilehash: e64e4a3baf48ecd5b16bf5a141455152b7a486f5


---
# <a name="cancel-your-azure-subscription"></a>取消 Azure 订阅
可以以帐户管理员身份取消 Azure 订阅。 取消订阅后，你将无法访问 Azure 服务和资源。

在取消订阅之前，请执行以下操作：

* 备份数据。 例如，若要在 Azure 存储或 SQL 中存储数据，请下载一个副本。 如果有虚拟机，请在本地保存该虚拟机的映像。
* 关闭服务。 转到[管理门户中的资源页](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)，**停止**任何正在运行的虚拟机、应用程序或其他服务。
* 考虑迁移数据。 请参阅[将资源移到新资源组或订阅中](./azure-resource-manager/resource-group-move-resources.md)。

取消付费的 [Azure 支持计划](https://azure.microsoft.com/support/plans/)后，仍会按月向用户收取余下 6 个月的费用。

## <a name="cancel-subscription-via-the-azure-portal"></a>通过 Azure 门户取消订阅
1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。

   > [!NOTE]
   > 只有帐户管理员可以取消订阅。 有关如何找出谁是订阅的帐户管理员的详细信息，请参阅[常见问题](billing-subscription-transfer.md#faq)。
   >
   >

2. 在“中心”菜单上，选择“订阅”。

    ![订阅按钮](./media/billing-download-azure-invoice-daily-usage-date/submenu.png)

3. 在“订阅”边栏选项卡中，选择要取消的订阅，然后单击“取消订阅”启动取消“边栏”选项卡。

    ![cancel_ibiza](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
4. 输入订阅名称并选择取消原因，然后单击“取消”边栏选项卡底部的“取消订阅”按钮。

> [!NOTE]
> 取消操作反映在门户中最多需要 10 分钟，但系统会立即停止计费。
>
>

## <a name="cancel-subscription-via-the-azure-account-center"></a>通过 Azure 帐户中心取消订阅
1. 以帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/subscriptions)。
2. 在“单击订阅以查看详细信息和使用情况”下面，选择要取消的订阅。

    ![selectsub](./media/billing-how-to-cancel-azure-subscription/Selectsub.png)
3. 在页面右侧，选择“取消订阅”。

    ![cancelsub](./media/billing-how-to-cancel-azure-subscription/cancelsub.png)
4. 选择“是，取消我的订阅”。

    ![cancelbox](./media/billing-how-to-cancel-azure-subscription/cancelbox.png)
5. 单击 ![checkbutton](./media/billing-how-to-cancel-azure-subscription/checkbutton.png) 关闭对话框窗口并返回订阅页。

用户取消订阅以后，我们会先等待 90 天，然后才会永久删除用户的数据，这样做是考虑到用户可能需要访问这些数据，或者改变了主意。 有关详细信息，请参阅 [Microsoft Trust Center - How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)（Microsoft 信任中心 - 我们如何管理用户的数据）。

## <a name="reactivate-subscription"></a>重新激活订阅
如果意外删除了即用即付订阅，可[在帐户中心重新激活订阅](billing-subscription-become-disable.md)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。



<!--HONumber=Dec16_HO2-->



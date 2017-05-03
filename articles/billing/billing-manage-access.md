---
title: "使用角色管理对 Azure 计费的访问 | Microsoft Docs"
description: 
services: 
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: vikdesai
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 3453e80246393f15cf0ee2eb29b2fb1d53f77078
ms.lasthandoff: 04/26/2017


---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>使用基于角色的访问控制管理对 Azure 计费信息的访问

可通过向订阅分配以下用户角色授予团队成员访问 Azure 计费信息的权限：帐户管理员、服务管理员、协同管理员、所有者、参与者、读取者和计费读取者。 他们将有权访问 [Azure 门户](https://portal.azure.com/)中的计费信息，并可使用[计费 API](billing-usage-rate-card-overview.md) 以编程方式获取发票（加入后）和用法详细信息。 若要深入了解谁可授予角色，以及哪些角色可执行什么操作，请参阅 [Azure RBAC 中的角色](../active-directory/role-based-access-built-in-roles.md)。

## <a name="opt-in"></a>允许其他用户访问发票

帐户管理员必须使用 [Azure 门户](https://portal.azure.com/)加入，允许通过 API 访问其他用户的发票。

1. 作为帐户管理员，请从 Azure 门户的[“订阅”边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择你的订阅。

1. 选择“发票”，然后“访问发票”。

1. 打开访问。

通过选择加入，允许订阅的服务管理员、协同管理员、所有者、参与者、读取者和计费读取者在 Azure 门户中下载 PDF 发票。 但是，早于 2016 年 12 月的发票目前仅供帐户管理员查看。

帐户管理员还可配置通过电子邮件发送发票。 若要了解详细信息，请参阅[在电子邮件中获取发票](billing-download-azure-invoice-daily-usage-date.md)。

## <a name="adding-users-to-the-billing-reader-role"></a>将用户添加到计费读取者角色

计费读取者角色可按只读方式访问 Azure 门户中的订阅计费信息，无权访问 VM 和存储帐户等服务。 将计费读取者角色分配给需访问订阅计费信息的人员，而不是需要 Azure 服务管理功能的人员。 此角色适用于组织中仅执行 Azure 订阅的财务和成本管理的用户。

1. 从 Azure 门户的[“订阅”边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择订阅。

1. 选择“访问控制(IAM)”，然后单击“添加”。

    ![在“订阅”边栏选项卡中显示 IAM 的屏幕截图](./media/billing-manage-access/select-iam.PNG)

1. 在“选择角色”页面中选择“计费读取者”。

    ![在弹出窗口视图中显示计费读取者的屏幕快照](./media/billing-manage-access/select-roles.PNG)

1. 键入想邀请的用户的电子邮件，然后单击“确定”发送邀请。

    ![显示输入电子邮件进行邀请的屏幕截图](./media/billing-manage-access/add-user.PNG)

1. 按照邀请电子邮件中的说明，以计费读取者身份登录。

    ![显示计费读取者可在 Azure 门户中所见内容的屏幕截图](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> 计费读取者功能处于预览状态，且仅适用于 Azure 全局云。

## <a name="adding-users-to-other-roles"></a>将用户添加到其他角色

其他角色的用户（如所有者或参与者）不仅可访问计费信息，还可访问 Azure 服务。 若要管理这些角色，请参阅[添加或更改管理订阅或服务的 Azure 管理员角色](billing-add-change-azure-subscription-administrator.md)。

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>谁可以访问[帐户中心](https://account.windowsazure.com)？

只有帐户管理员可登录到帐户中心。 帐户管理员是订阅的合法所有者。 默认情况下，注册或购买 Azure 订阅的人员即为帐户管理员，除非[订阅所有权被转让](billing-subscription-transfer.md)给了其他人。 帐户管理员可以创建订阅、取消订阅、更改订阅的账单地址和管理订阅的访问策略。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

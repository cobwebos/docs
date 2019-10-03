---
title: 对 Azure Active Directory B2C 的支持 | Microsoft Docs
description: 如何提出针对 Azure Active Directory B2C 的支持请求。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f7f2217677b81c6f3e87eaa2612880adf3b499c2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064939"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C：文件支持请求
您可以使用以下步骤在 Azure 门户上为 Azure Active Directory B2C （Azure AD B2C）提供支持请求：

1. 从 B2C 租户切换到另一个拥有与其关联的 Azure 订阅的租户。 通常，后者是员工租户或在注册 Azure 订阅时创建的默认租户。 若要了解详细信息，请参阅 [Azure 订阅如何与 Azure AD 关联](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

    ![突出显示了租户选择 Azure 门户](./media/active-directory-b2c-support/support-switch-dir.png)

1. 切换租户后，请单击“帮助 + 支持”。

    !["帮助 + 支持" 磁贴突出显示 Azure 门户](./media/active-directory-b2c-support/support-support.png)

1. 单击“新建支持请求”。

    ![Azure 门户中突出显示的新的支持请求磁贴](./media/active-directory-b2c-support/support-new.png)

1. 在“基础知识”边栏选项卡中，使用这些详细信息，并单击“下一步”。

    * “问题类型”为“技术”。
    * 选择适当的“订阅”。
    * “服务”为“Active Directory”。
    * 选择适当的“支持计划”。 如果没有计划，可以[在此](https://azure.microsoft.com/support/plans/)注册一个。

     !["基本信息" 页中突出显示 "下一步" 按钮 Azure 门户](./media/active-directory-b2c-support/support-basics.png)

1. 在“问题”边栏选项卡中，使用这些详细信息，并单击“下一步”。

    * 选择适当的“严重性”级别。
    * “问题类型”为“B2C”。
    * 选择适当的“类别”。
    * 在“详细信息”字段中描述问题。 提供详细信息，例如 B2C 租户名称、问题描述、错误消息、关联 ID（如果可用）等。
    * 在“时间范围”字段中，提供问题发生的日期和时间（包括时区）。
    * 在“文件上传”下，上传所有你认为有助于解决问题的屏幕截图和文件。

     ![Azure 门户中突出显示 "下一步" 按钮的问题页](./media/active-directory-b2c-support/support-problem.png)

1. 在“联系人信息”边栏选项卡中，添加联系人信息。 单击“创建”。

    ![门户中突出显示的 "创建" 按钮的 "联系人信息" 页](./media/active-directory-b2c-support/support-contact.png)

1. 提交支持请求后，可以单击启动板上的“帮助 + 支持”，并点击“管理支持请求”进行监控。

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>已知问题：在 B2C 租户的上下文中提出支持请求

如果遗漏了上述步骤 2，而尝试在 B2C 租户的上下文中创建支持请求，则会看到以下错误。

> [!IMPORTANT]
> 请勿尝试在 B2C 租户中注册新的 Azure 订阅。

![你没有在 Azure 门户中显示的订阅错误](./media/active-directory-b2c-support/support-no-sub.png)

---
title: 配置授予数据访问权限所需的安全性 - Azure 时序见解预览版 | Microsoft Docs
description: 了解如何在 Azure 时序见解预览版环境中配置安全性和权限并管理数据访问策略。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: 1c8f14bb1bca082a9d887e5d6d88aec213448c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254347"
---
# <a name="grant-data-access-to-an-environment"></a>授予对环境的数据访问权限

本文讨论了两种类型的 Azure 时序见解预览版访问策略。

> [!TIP]
> 读取 Azure 活动目录应用注册步骤的[身份验证和授权](time-series-insights-authentication-and-authorization.md)。

## <a name="sign-in-to-time-series-insights"></a>登录到 Azure 时序见解

1. 登录到 Azure[门户](https://portal.azure.com/)。
1. 查找时序见解环境。 在“搜索”框中输入 `Time Series`。**** 在搜索结果中选择“时序环境”****。
1. 从列表中选择时序见解环境。

## <a name="grant-data-access"></a>授予数据访问权限

遵循以下步骤向用户主体授予数据访问权限。

1. 选择“数据访问策略”，然后选择“+ 添加”********。

    [![选择并添加数据访问策略](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. 选择“选择用户”。**** 搜索用户名称或电子邮件地址，查找要添加的用户。 选择“选择”以确认选择****。

    [![选择要添加的用户](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. 选择“选择角色”。**** 为用户选择相应的访问角色：

    * 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”****。

    * 否则请选择“读者”，允许用户查询环境中的数据，以及在环境中保存个人的非共享查询****。

   选择 **"确定"** 以确认角色选择。

    [![确认所选角色](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. 在“选择用户角色”页中，选择“确定”********。

    [![在"选择用户角色"页上选择"确定"](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. 确认“数据访问策略”页列出了用户和每个用户的角色****。

    [![验证正确的用户和角色](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>提供从另一个 Azure AD 租户进行来宾访问的权限

`Guest` 角色不是管理角色。 它是一个术语，是指从一个租户被邀请到另一个租户的帐户。 在将来宾帐户邀请到租户目录后，它就可以像任何其他帐户一样拥有相同的访问控制。 可以使用“访问控制(IAM)”边栏选项卡授予对时序见解环境的管理访问权限。 也可以通过“数据访问策略”边栏选项卡授予对环境中数据的访问权限。 若要详细了解 Azure Active Directory (Azure AD) 租户来宾访问权限，请阅读[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。

请按照以下步骤将来宾对时序见解环境的访问权限授予另一个租户的 Azure AD 用户。

1. 选择“数据访问策略”，然后选择“+ 邀请”********。

    [![选择数据访问警察，然后 + 邀请](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. 输入要邀请的用户的电子邮件地址。 此电子邮件地址必须与 Azure AD 关联。 你可以根据情况在邀请中包括个人消息。

    [![输入电子邮件地址以查找所选用户](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. 查找屏幕上显示的确认气泡。

    [![查找要显示的确认气泡](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. 选择“选择用户”。**** 搜索已邀请的来宾用户的电子邮件地址，找到要添加的用户。 然后单击“选择”以确认选择****。

    [![选择用户并确认选择](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. 选择“选择角色”。**** 为来宾用户选择相应的访问角色：

    * 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”****。

    * 否则请选择“读者”，允许用户查询环境中的数据，以及在环境中保存个人的非共享查询****。

   选择 **"确定"** 以确认角色选择。

    [![确认角色选择](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. 在“选择用户角色”页中，选择“确定”********。

1. 确认“数据访问策略”**** 页列出了来宾用户和每个来宾用户的角色。

    [![验证用户和角色是否正确分配](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. 现在，来宾用户会在上面指定的电子邮件地址收到邀请电子邮件。 来宾用户会选择“开始”来确认接受邀请并连接到 Azure Cloud。****

    [![来宾选择"开始接受"](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. 选择 **"开始"** 后，来宾用户将显示一个与管理员组织关联的权限框。 通过选择“接受”授予权限后，来宾用户就会登录。****

    [![来宾审核权限并接受](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. 管理员与其来宾[共享环境 URL](time-series-insights-parameterized-urls.md)。

1. 当来宾用户登录到你用来邀请他们的电子邮件地址并接受邀请后，系统就会将其定向到 Azure 门户。 

1. 来宾现在可以使用管理员提供的环境 URL 访问共享环境。 来宾将该 URL 输入其 Web 浏览器即可立刻进行访问。

1. 在时序资源管理器的右上角选择其配置文件图标后，将向来宾用户显示管理员的租户。

    [![insights.azure.com上的阿凡达选择](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    来宾用户在选择管理员的租户以后，即可选择共享的时序见解环境。 
    
    现在，他们拥有了与你在**步骤 5** 中提供的角色相关联的所有功能。

    [![来宾用户从下拉列表中选择 Azure 租户](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 了解[如何将 Azure 事件中心事件源添加到](./time-series-insights-how-to-add-an-event-source-eventhub.md)时序见解环境。

* 将[事件发送到事件源](./time-series-insights-send-events.md)。

* [在时序见解预览版资源管理器中查看环境](./time-series-insights-update-explorer.md)。

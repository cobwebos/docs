---
title: 在 Azure 中使用 RBAC 管理外部用户的访问权限 | Microsoft Docs
description: 了解如何在 Azure 中使用基于角色的访问控制 (RBAC) 管理组织外部用户的访问权限。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 98eb104981051bd5e7440954470960977b38286d
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296964"
---
# <a name="manage-access-for-external-users-using-rbac"></a>使用 RBAC 管理外部用户的访问权限

基于角色的访问控制 (RBAC) 可以为大型组织和中小企业提供更好的安全管理，与中小企业合作的外部协作者、供应商或自由职业者需要访问你环境中的特定资源，但不一定需要访问整个基础架构或任何与计费相关的区域。 RBAC 允许灵活拥有管理员帐户（订阅级别的服务管理员角色）管理的 Azure 订阅，并邀请多个用户在同一个订阅下工作，但不为他们分配任何管理权限。

> [!NOTE]
> 从 Office 365 管理员中心预配的 Office 365 订阅或 Azure Active Directory 许可证（例如：Azure Active Directory 访问权限）不符合 RBAC 的使用条件。

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>在订阅范围分配 RBAC 角色

使用 RBAC 时，有两个（但不限于）常见的示例：

* 邀请组织外部的用户（不属于管理员用户的 Azure Active Directory 租户）管理特定的资源或整个订阅
* 与组织内部的、但属于不同团队或组的用户（属于用户的 Azure Active Directory 租户）合作，这些团队或组需要对环境中的整个订阅或者特定资源组或资源范围拥有精细访问权限

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>为 Azure Active Directory 外部的用户授予订阅级访问权限

RBAC 角色只能由订阅的“所有者”授予。 因此，管理员必须使用已预先分配有此角色或已创建 Azure 订阅的用户身份登录。

在 Azure 门户中以管理员身份登录后，选择“订阅”，并选择所需的订阅。
![Azure 门户中的订阅边栏选项卡](./media/role-assignments-external-users/0.png)默认情况下，如果管理员用户购买了 Azure 订阅，则该用户会显示为“帐户管理员”，即订阅角色。 有关 Azure 订阅角色的详细信息，请参阅[添加或更改管理订阅或服务的 Azure 管理员角色](../billing/billing-add-change-azure-subscription-administrator.md)。

在此示例中，用户“alflanigan@outlook.com”是 AAD 租户“默认租户 Azure”中“免费试用”订阅的“所有者”。 由于此用户是使用最初的 Microsoft 帐户“Outlook”（Microsoft 帐户 = Outlook、Live 等等）创建 Azure 订阅的用户，因此，添加到此租户的其他所有用户的默认域名将是“\@alflaniganuoutlook.onmicrosoft.com”。 根据设计，新域的语法格式是将创建租户的用户的用户名和域名组合在一起，并加上扩展 **.onmicrosoft.com**。
此外，在为新租户添加并验证自定义域名后，用户可以在租户中使用该域名登录。 有关如何在 Azure Active Directory 租户中验证自定义域名的详细信息，请参阅[将自定义域名添加到目录](/active-directory/active-directory-add-domain)。

在此示例中，“默认租户 Azure”目录仅包含域名为“\@alflanigan.onmicrosoft.com”的用户。

选择订阅后，管理员用户必须单击“访问控制(IAM)”，并单击“添加新角色”。

![Azure 门户中的访问控制 IAM 功能](./media/role-assignments-external-users/1.png)

![在 Azure 门户的访问控制 IAM 功能中添加新用户](./media/role-assignments-external-users/2.png)

下一步是选择要分配的角色，以及要将 RBAC 角色分配到的用户。 在“角色”下拉菜单中，管理员用户只会看到 Azure 中提供的内置 RBAC 角色。 有关每个角色及其可分配范围的更详细说明，请参阅[内置角色](built-in-roles.md)。

然后，管理员用户需要添加外部用户的电子邮件地址。 预期的行为不在现有租户中显示外部用户。 邀请外部用户后，“订阅”>“访问控制(IAM)”下面会显示该用户，以及当前在订阅范围分配有 RBAC 角色的所有当前用户。

![将权限添加到新的 RBAC 角色](./media/role-assignments-external-users/3.png)

![列出订阅级别的 RBAC 角色](./media/role-assignments-external-users/4.png)

已邀请用户“chessercarlton@gmail.com”成为“免费试用”订阅的“所有者”。 发送邀请后，外部用户将收到包含激活链接的电子邮件确认。
![RBAC 角色的电子邮件邀请](./media/role-assignments-external-users/5.png)

由于在组织外部，新用户在“默认租户 Azure”目录中没有任何现有的属性。 在外部用户同意记录在与其分配有角色的订阅关联的目录中后，会创建这些属性。

![RBAC 角色的电子邮件邀请消息](./media/role-assignments-external-users/6.png)

从现在开始，该外部用户会显示在 Azure Active Directory 租户中，可以在 Azure 门户中查看该用户。

![Azure Active Directory Azure 门户中的用户边栏选项卡](./media/role-assignments-external-users/7.png)

在“用户”视图中，可以通过 Azure 门户中的不同图标类型识别外部用户。

但是，在“订阅”范围向外部用户授予“所有者”或“参与者”访问权限并不允许他们访问管理员用户的目录，除非“全局管理员”允许访问。 在用户属性中，可以标识包含“成员”和“来宾”这两个通用参数的“用户类型”。 成员是已在目录中注册的用户，而来宾是来自外部源的、受邀加入目录的用户。 有关详细信息，请参阅 [Azure Active Directory 管理员如何添加 B2B 协作用户](../active-directory/active-directory-b2b-admin-add-users.md)。

> [!NOTE]
> 请确保在门户中输入凭据后，外部用户选择要登录到的正确目录。 同一个用户可以访问多个目录，并且可以选择其中的一个目录：在 Azure 门户中单击右上角的用户名，并从下拉列表中选择相应的目录。

成为目录中的来宾后，外部用户可以管理 Azure 订阅的所有资源，但无法访问该目录。

![Azure Active Directory Azure 门户中受限的访问权限](./media/role-assignments-external-users/9.png)

Azure Active Directory 与 Azure 订阅之间不像其他 Azure 资源（例如：虚拟机、虚拟网络、Web 应用、存储等）与 Azure 订阅之间一样存在子-父关系。 后者都是在 Azure 订阅下创建、管理和计费的，而 Azure 订阅用于管理对 Azure 目录的访问。 有关详细信息，请参阅 [Azure 订阅与 Azure AD 的关系](/active-directory/active-directory-how-subscriptions-associated-directory)。

在所有内置 RBAC 角色中，“所有者”和“参与者”提供对环境中所有资源的完全管理访问权限，两者的区别在于，“参与者”无法创建和删除新的 RBAC 角色。 其他内置角色（例如“虚拟机参与者”）只提供对按名称指定的资源的完全管理访问权限，不管这些角色是在哪个**资源组**中创建的。

在订阅级别分配“虚拟机参与者”内置 RBAC 角色意味着分配有该角色的用户：

* 可以查看所有虚拟机，不管这些虚拟机的部署日期及其所属的资源组是什么
* 对订阅中的虚拟机拥有完全管理访问权限
* 无法查看订阅中的其他任何资源类型
* 从计费角度无法操作任何更改

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>将内置的 RBAC 角色分配到外部用户

针对此项测试中的不同方案，外部用户“alflanigan@gmail.com”将添加为“虚拟机参与者”。

![虚拟机参与者内置角色](./media/role-assignments-external-users/11.png)

使用此内置角色的此外部用户的正常行为是仅查看和管理虚拟机，以及部署时所需的仅限 Resource Manager 的相邻资源。 根据设计，这些受限角色只提供对在 Azure 门户中创建的相应资源的访问权限。

![Azure 门户中的虚拟机参与者角色概览](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>为同一目录中的用户授予订阅级访问权限

无论是从授予 RBAC 角色的管理角度，还是将授予用户对角色的访问权限的角度来看，流程都与添加外部用户相同。 此处的差别在于，受邀用户不会收到任何电子邮件邀请，因为订阅中的所有资源范围只有在登录后才会显示在仪表板中。

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>在资源组范围分配 RBAC 角色

对于外部和内部（属于同一个目录）这两种用户类型，在**资源组**范围分配 RBAC 角色的过程与在订阅级别分配角色的过程相同。 分配有 RBAC 角色的用户只能通过 Azure 门户中的“资源组”图标查看其环境中他们有权访问的资源组。

## <a name="assign-rbac-roles-at-the-resource-scope"></a>在资源范围分配 RBAC 角色

在 Azure 中的资源范围分配 RBAC 角色的过程，与在订阅级别或资源组级别分配该角色的过程相同，这两种方案都遵循相同的工作流。 同样，分配有 RBAC 角色的用户只能在“所有资源”选项卡或直接在仪表板中查看他们有权访问的项。

对于资源组范围或资源范围的 RBAC，一个重要的方面是，用户必须确保登录到正确的目录。

![Azure 门户中的目录登录](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>为 Azure Active Directory 组分配 RBAC 角色

在 Azure 中的三个不同范围使用 RBAC 的所有方案都提供以已分配用户身份管理和部署各种资源的特权，而无需管理个人订阅。 不管 RBAC 角色是针对订阅、资源组还是资源范围分配的，已分配用户创建的其他所有资源都会根据用户有权访问的某个 Azure 订阅进行计费。 这样，对整个 Azure 订阅拥有计费管理员权限的用户便可以获得完整的消耗概况，不管资源由谁管理。

对于大型组织，可通过不同的方式针对 Azure Active Directory 组应用 RBAC 角色，同时考虑到管理员用户是否想要针对团队或整个部门而不是每个用户授予精细访问权限，因此可将此方案视为一个极具时间和管理效率的选项。 为了演示此示例，我们已在订阅级别将“参与者”角色添加到租户中的某个组。

![为 AAD 组添加 RBAC 角色](./media/role-assignments-external-users/14.png)

这些组是只在 Azure Active Directory 中预配和管理的安全组。


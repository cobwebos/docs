---
title: Azure AD 权限管理中更改访问包的请求设置-Azure Active Directory
description: 了解如何在 Azure Active Directory 权限管理中更改访问包的请求设置。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7b70e8a408c22be0331bfd0dcbe01830b072ab8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449971"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 权限管理中更改访问包的请求设置

访问包管理者可以通过编辑策略或添加新策略随时更改可以请求访问包的用户， 本文介绍如何更改现有访问包的请求设置。

## <a name="choose-between-one-or-multiple-policies"></a>在一个或多个策略之间进行选择

指定谁可以请求访问包的方式是使用策略。 在创建新策略或编辑访问包中的现有策略之前，需要确定访问包需要多少个策略。 

创建访问包时，请指定用于创建策略的请求设置。 大多数访问包将具有单个策略，但单个访问包可以具有多个策略。 如果希望允许使用不同的请求和审批设置为不同的用户组授予分配，可为单个访问包创建多个策略。 

例如，单个策略不可用于将内部和外部用户分配到同一个访问包。 但是，你可以在同一个访问包中创建两个策略，一个用于内部用户，另一个用于外部用户。 如果对某个用户应用了多个策略，当用户请求选择要分配到的策略时，系统会提示他们。 下图显示了具有两个策略的访问包。

![访问包中的多个策略](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>我将需要多少个策略？

| 方案 | 策略数 |
| --- | --- |
| 我希望目录中的所有用户对访问包具有相同的请求和审批设置 | 一个 |
| 我希望某些已连接组织中的所有用户能够请求访问包 | 一个 |
| 我希望允许目录中的用户以及目录外的用户请求访问包 | 多种 |
| 我希望为某些用户指定不同的审批设置 | 多种 |
| 我希望某些用户访问包分配过期，而其他用户可以延长访问权限 | 多种 |

有关多个策略适用时使用的优先级逻辑的信息，请参阅[多个策略](entitlement-management-troubleshoot.md#multiple-policies
)。

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>打开现有的访问包，并添加新的请求设置策略

如果有一组用户应具有不同的请求和审批设置，则可能需要创建新策略。 按照以下步骤开始将新策略添加到现有的访问包：

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 依次单击“策略”、“添加策略”。********

1. 你将从 " **基本** 信息" 选项卡开始。键入策略的名称和描述。

    ![创建策略并输入名称和说明](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. 单击“下一步”，打开“请求”选项卡   。

1. 更改 **可以请求访问** 设置的用户。 使用以下部分中的步骤将设置更改为以下选项之一： 
    - [适用于目录中的用户](#for-users-in-your-directory) 
    - [适用于不在目录中的用户](#for-users-not-in-your-directory)
    - [无(仅限管理员直接分配)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>适用于目录中的用户

如果你希望目录中的用户可请求此访问包，请执行以下步骤。 定义请求策略时，可以指定单个用户，也可以指定用户组（通常做法）。 例如，组织可能已经有一个组（例如“所有员工”）。****  如果将该组添加到可以请求访问权限的用户的策略中，则该组的任何成员都可以请求访问权限。

1. 在“可以请求访问的用户”部分，单击“你目录中的用户”。********

    选择此选项后，会出现新的选项以进一步优化目录中哪些用户可以请求此访问包。

    ![访问包 - 请求 - 你目录中的用户](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. 选择以下选项之一：

    |  |  |
    | --- | --- |
    | **特定用户和组** | 如果只希望目录中的指定用户和组可以请求此访问包，请选择此选项。 |
    | **所有成员（不包括来宾）** | 如果希望目录中的所有成员用户都可以请求此访问包，请选择此选项。 此选项不包括你可能已邀请到目录中的任何来宾用户。 |
    | **所有用户（包括来宾）** | 如果希望目录中的所有成员用户和来宾用户都可以请求此访问包，请选择此选项。 |

    来宾用户是指通过 [Azure AD B2B](../external-identities/what-is-b2b.md) 邀请到目录中的外部用户。 有关成员用户和来宾用户之间差异的详细信息，请参阅 [Azure Active Directory 中的默认用户权限是什么？](../fundamentals/users-default-permissions.md)。

1. 如果已选择“特定用户和组”，请单击“添加用户和组” 。

1. 在“选择用户和组”窗格中，选择要添加的用户和组。

    ![访问包 - 请求 - 选择用户和组](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. 单击“选择”以添加用户和组。****

1. 如果需要批准，请使用 [Azure AD 授权管理 "中的访问包的更改审批设置中](entitlement-management-access-package-approval-policy.md) 的步骤来配置审批设置。

1. 请参阅 [Enable requests](#enable-requests) 部分。
 
## <a name="for-users-not-in-your-directory"></a>适用于不在目录中的用户

 “不在目录中的用户”是指位于其他 Azure AD 目录或域中的用户。 这些用户可能尚未被邀请到目录中。 Azure AD 目录必须配置为允许”协作限制”中的邀请。 有关详细信息，请参阅[启用 B2B 外部协作并管理谁可以邀请来宾](../external-identities/delegate-invitations.md)。

> [!NOTE]
> 将为不是目录中的其请求已审批或自动审批的用户创建来宾用户帐户。 将邀请来宾，但他们不会收到邀请电子邮件。 传递其访问包分配时，他们将收到电子邮件。 默认情况下，当来宾用户不再有任何访问包分配时（因为他们的上次分配已过期或已取消），将会阻止该来宾用户帐户登录并随后将其删除。 如果希望无限期地在目录中保留来宾用户（即使他们没有任何访问包分配），可以更改权利管理配置的设置。 有关来宾用户对象的详细信息，请参阅 [Azure Active Directory B2B 协作用户的属性](../external-identities/user-properties.md)。

如果要允许不在目录中的用户请求此访问包，请执行以下步骤：

1. 在“可以请求访问的用户”部分，单击“不在目录中的用户” 。

    选择此选项时，将显示新选项。

    ![访问包 - 请求 - 不在目录中的用户](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. 选择以下选项之一：

    |  |  |
    | --- | --- |
    | **特定的已连接的组织** | 如果要从管理员之前添加的组织列表中选择，请选择此选项。 来自选定组织的所有用户都可以请求此访问包。 |
    | **所有已配置的已连接组织** | 如果所有已配置连接的组织中的所有用户都可以请求此访问包，请选择此选项。 只有已配置的连接的组织中的用户可以请求从所有配置的组织向用户显示的访问包。 |
    | **所有用户（所有已连接的组织 + 任何新外部用户）** | 如果 internet 上的任何用户应该能够请求此访问包，请选择此选项。  如果用户不属于目录中的已连接组织，则在请求包时将自动为其创建连接的组织。 自动创建的连接的组织将处于 "已 **建议** " 状态。 有关建议状态的详细信息，请参阅 [已连接组织的状态属性](entitlement-management-organization.md#state-properties-of-connected-organizations)。 |

    已连接的组织是与你有关系的外部 Azure AD 目录或域。

1. 如果选择了“特定连接的组织”，请单击“添加目录”，从管理员之前添加的已连接的组织列表中进行选择 。

1. 键入要搜索的之前已连接的组织的名称或域名。

    ![访问包 - 请求 - 选择目录](./media/entitlement-management-access-package-request-policy/select-directories.png)

    如果要与之协作的组织不在列表中，则可以要求管理员将其添加为已连接的组织。 有关详细信息，请参阅[添加已连接的组织](entitlement-management-organization.md)。

1. 选择所有已连接的组织后，单击“选择”。

    > [!NOTE]
    > 来自选定已连接的组织的所有用户都将可以请求此访问包。 这包括来自与组织关联的所有子域的 Azure AD 中的用户，除非这些域被 Azure B2B 允许或拒绝列表阻止。 有关详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](../external-identities/allow-deny-list.md)。

1. 如果需要批准，请使用 [Azure AD 授权管理 "中的访问包的更改审批设置中](entitlement-management-access-package-approval-policy.md) 的步骤来配置审批设置。
 
1. 请参阅 [Enable requests](#enable-requests) 部分。

## <a name="none-administrator-direct-assignments-only"></a>无(仅限管理员直接分配)

如果希望绕过访问请求，并允许管理员直接将特定用户分配到访问包，请执行这些步骤。 用户无需请求访问包。 仍可以设置生命周期设置，但没有请求设置。

1. 在“可以请求访问权限的用户”部分，单击“无（仅限管理员直接分配）” 。

    ![访问包 - 请求 - 无（仅限管理员直接分配）](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    创建访问包后，可以直接将特定的内部和外部用户分配到该访问包。 如果指定外部用户，将在目录中创建来宾用户帐户。 有关直接分配用户的详细信息，请参阅[查看、添加和删除访问包的分配](entitlement-management-access-package-assignments.md)。

1. 跳到 " [启用请求](#enable-requests) " 部分。


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>打开和编辑请求设置的现有策略

若要更改访问包的请求和审批设置，需要打开相应的策略。 按照以下步骤打开并编辑访问包的请求设置：

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 单击“策略”，然后单击要编辑的策略。****

    页面底部会打开“策略详细信息”窗格。

    ![访问包 -“策略详细信息”窗格](./media/entitlement-management-shared/policy-details.png)

1. 单击“编辑”以编辑策略。****

    ![访问包 - 编辑策略](./media/entitlement-management-shared/policy-edit.png)

1. 单击 " **请求** " 选项卡以打开请求设置。

1. 使用之前部分中的步骤，根据需要更改请求设置。

1. 请参阅 [Enable requests](#enable-requests) 部分。

## <a name="enable-requests"></a>启用请求

1. 如果希望访问包立即可供请求策略中的用户使用以进行请求，请将“启用”切换开关移动到“是”。

    创建完访问包后，将来始终可以启用该策略。

    如果选择了“无（仅限管理员直接分配）”，并且将“启用”设置为“否”，则管理员无法直接分配此访问包 。

    ![访问包 - 策略 - 启用策略设置](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. 单击“下一步”  。

1. 如果希望在请求访问访问包时要求请求程序提供其他信息，请使用 []() 配置请求程序信息 (预览版) 中的步骤。

1. 配置生命周期设置。

1. 如果正在编辑策略，请单击“更新”。 如果正在编辑新策略，请单击“创建”。

## <a name="next-steps"></a>后续步骤

- [更改访问包的审批设置](entitlement-management-access-package-approval-policy.md)
- [更改访问包的生命周期设置](entitlement-management-access-package-lifecycle-policy.md)
- [查看访问包的请求](entitlement-management-access-package-requests.md)

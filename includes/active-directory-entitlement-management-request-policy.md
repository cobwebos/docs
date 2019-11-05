---
title: include 文件
description: include 文件
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 6f2b5eb96eeb1c4b7d07219d5fe54a8a0ca9e28a
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73412946"
---
## <a name="for-users-in-your-directory"></a>适用于目录中的用户

如果希望允许目录中的用户请求此访问包，请执行以下步骤。 定义请求策略时，可以指定单个用户或更常见的用户组。 例如，组织可能已经有一个组（例如“所有员工”）。  如果将该组添加到可以请求访问权限的用户的策略中，则该组的任何成员都可以请求访问权限。

1. 在“可以请求访问的用户”部分，单击“你目录中的用户”。

    如果选择此选项，则会显示新的选项，进一步优化目录中的哪些用户可以请求此访问包。

    ![访问包-请求-适用于目录中的用户](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. 选择以下选项之一：

    |  |  |
    | --- | --- |
    | **特定用户和组** | 如果你只希望你指定的目录中的用户和组能够请求此访问包，请选择此选项。 |
    | **所有成员（不包括来宾）** | 如果希望目录中的所有成员用户都能够请求此访问包，请选择此选项。 此选项不包括你可能已邀请到你的目录的任何来宾用户。 |
    | **所有用户（包括来宾）** | 如果希望目录中的所有成员用户和来宾用户都能够请求此访问包，请选择此选项。 |

    来宾用户指的是已通过[AZURE AD B2B](../articles/active-directory/b2b/what-is-b2b.md)邀请加入你的目录的外部用户。 有关成员用户和来宾用户之间的差异的信息，请参阅[Azure Active Directory 中的默认用户权限是什么？](../articles/active-directory/fundamentals/users-default-permissions.md)。

1. 如果选择了 "**特定用户和组**"，请单击 "**添加用户和组**"。

1. 在“选择用户和组”窗格中，选择要添加的用户和组。

    ![访问包-请求-选择用户和组](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. 单击“选择”以添加用户和组。

1. 向下跳到 "[审批](#approval)" 部分。

## <a name="for-users-not-in-your-directory"></a>适用于不在目录中的用户

如果要允许不在目录中的用户请求此访问包，请执行以下步骤。 **不在你的目录中的用户**指的是另一个 Azure AD 目录或域中的用户，可能尚未邀请到你的目录。 必须将 Azure AD 目录配置为允许在**协作限制**中加入邀请。 有关详细信息，请参阅[启用 B2B 外部协作和管理可以邀请来宾的人员](../articles/active-directory/b2b/delegate-invitations.md)。

> [!NOTE]
> 将为不是目录中的其请求已审批或自动审批的用户创建来宾用户帐户。 将邀请来宾，但他们不会收到邀请电子邮件。 传递其访问包分配时，他们将收到电子邮件。 默认情况下，当来宾用户不再有任何访问包分配时（因为他们的上次分配已过期或已取消），将会阻止该来宾用户帐户登录并随后将其删除。 如果希望无限期地在目录中保留来宾用户（即使他们没有任何访问包分配），可以更改权利管理配置的设置。 有关来宾用户对象的详细信息，请参阅[AZURE ACTIVE DIRECTORY B2B 协作用户的属性](../articles/active-directory/b2b/user-properties.md)。

1. 在 "**可请求访问的用户**" 部分中，单击 "**对于不在你的目录中的用户**"。

    如果选择此选项，将显示新选项。

    ![访问包-请求-对于不在你的目录中的用户](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. 选择以下选项之一：

    |  |  |
    | --- | --- |
    | **特定连接的组织** | 如果要从管理员之前添加的组织列表中进行选择，请选择此选项。 所选组织的所有用户将能够请求此访问包。 |
    | **所有连接的组织** | 如果希望所有连接的组织中的所有用户都能够请求此访问包，请选择此选项。 |

    连接的组织是您与之有关系的外部 Azure AD 目录或域。

1. 如果选择了 "**特定连接的组织**"，请单击 "**添加目录**" 以从管理员之前添加的已连接组织列表中进行选择。

1. 键入名称或域名以搜索先前连接的组织。

    ![访问包-请求-选择目录](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    如果你想要与之合作的组织不在列表中，则可以要求管理员将其添加为连接的组织。 有关详细信息，请参阅[添加连接的组织](../articles/active-directory/governance/entitlement-management-organization.md)。

1. 选择所有连接的组织后，单击 "**选择**"。

    > [!NOTE]
    > 所选连接的组织中的所有用户都能够请求此访问包。 这包括与组织关联的所有子域 Azure AD 中的用户，除非这些域被 Azure B2B 允许或拒绝列表阻止。 有关详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](../articles/active-directory/b2b/allow-deny-list.md)。

1. 向下跳到 "[审批](#approval)" 部分。

## <a name="none-administrator-direct-assignments-only"></a>无(仅限管理员直接分配)

如果要跳过访问请求并允许管理员直接将特定用户分配到此访问包，请执行以下步骤。 用户无需请求访问包。 你仍可以设置生命周期设置，但没有请求设置。

1. 在 "**可请求访问的用户**" 部分中，单击 "**无（仅限管理员直接分配**"。

    ![访问包-请求-仅限管理员直接分配](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    创建访问包后，可以直接将特定的内部和外部用户分配到该访问包。 如果指定外部用户，将在目录中创建来宾用户帐户。 有关直接分配用户的信息，请参阅[查看、添加和删除访问包的分配](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)。

1. 向下跳到 "[启用请求](#enable-requests)" 部分。

## <a name="approval"></a>审批

在 "审批" 部分中，指定在用户请求此访问包时是否需要审批。 审批设置的工作方式如下：

- 只有一个选定的审批者或备用审批者需要批准请求。 不需要所有审批者审批。
- 审批决定以第一个评审请求的审批者为准。

按照以下步骤为之前选择的用户指定审批设置。

1. 若要要求对所选用户发起的请求进行审批，请将“需要审批”切换开关设置为“是”。 若要自动审批请求，请将切换开关设置为“否”。

1. 若要要求用户提供理由来请求访问包，请将 "**需要请求者对齐**方式" 切换为 **"是"** 。

    ![访问包-请求-批准设置](./media/active-directory-entitlement-management-request-policy/approval.png)

### <a name="single-stage-approval"></a>单阶段审批

1. 对于审批者，选择 "**经理为审批者**" 或**选择特定审批者**。

    ![访问包-请求-单阶段设置](./media/active-directory-entitlement-management-request-policy/approval-single-stage.png)

1. 如果选择了 "经理" 作为审批者，请单击 "**添加回退**" 以选择目录中的一个或多个用户或组作为备用审批者，以防权利管理找不到管理者。

    管理器由 Azure AD 的用户配置文件中的**manager**属性确定。 有关详细信息，请参阅[使用 Azure Active Directory 添加或更新用户的配置文件信息](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)。

1. 如果选择了 "选择特定审批者"，请单击 "**添加审批**者"，选择要作为审批者的目录中的一个或多个用户或组。

1. **必须在多少天内进行决定？** ，指定审批者必须查看此访问包的请求的天数。

    如果在此时间段内未批准请求，则会自动拒绝该请求。 用户需要提交访问包的另一个请求。

1. 若要要求用户提供要求访问包的理由，请将 "**需要审批者理由**" 设置为 **"是"** 。

    对齐对于其他审批者和请求者可见。

### <a name="alternate-approvers"></a>备用审批者

除了指定可批准请求的主要审批者，还可以指定备用审批者。 这将有助于确保请求在过期之前批准或拒绝（超时）。

通过指定备用审批者，如果主审批者无法批准或拒绝该请求，则会根据你在策略设置期间指定的转发计划，将挂起的请求转发到备用审批者。 他们会收到一封电子邮件，用于批准或拒绝挂起的请求。

将请求转发到备用审批者后，主审批者仍可批准或拒绝该请求。 备用审批者使用与主审批者相同的访问站点来批准或拒绝挂起的请求。

我们可以列出要成为主要审批者和备用审批者的人员或组。 请确保列出了要成为主要审批者和备用审批者的不同人员组。
例如，如果你将 Alice 和 Bob 列为主审批者，请将 Carol 和 Dave 列出为备用审批者。 使用以下步骤将备用审批者添加到 access 包：

1. 单击 "**显示高级请求设置**"。

    ![访问包-策略-显示高级请求设置](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. **如果未采取任何操作，请转发到备用审批者，则**设置为 **"是"** 。

1. 单击 "**添加备用审批者**"，然后从列表中选择备用审批者。

    ![访问包-策略-添加备用审批者](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. 在 "开始**多少天后转发到备用审批者**" 框中，放入审批者批准或拒绝请求所需的天数。 如果在请求持续时间之前没有审批者批准或拒绝请求，则请求将过期（超时），用户必须提交访问包的另一个请求。 

    请求持续时间达到半期后，只能将请求转发给备用审批者。 在此示例中，请求的持续时间为14天。 这意味着请求持续时间为第7天的半期。 因此，可以将请求转发到第8天之前。 此外，请求不能在请求持续时间的最后一天转发给备用审批者。 因此，在此示例中，可以转发的最新请求为第13天。

## <a name="enable-requests"></a>启用请求

1. 如果希望访问包立即可供请求策略中的用户使用，请单击 **"是"** 以启用。

    创建完访问包后，将来始终可以启用该策略。

    如果选择了 "**无" （仅限管理员直接分配）** ，并将 "启用" 设置为 "**否**"，则管理员将无法直接分配此访问包。

    ![访问包 - 策略 - 启用策略设置](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. 单击“下一步”。

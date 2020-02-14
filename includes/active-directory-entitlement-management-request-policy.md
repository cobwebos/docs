---
title: include 文件
description: include 文件
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 11/11/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: c73a62b2d5feeae42a5ea35c88073dd5fcc0d78c
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192871"
---
## <a name="for-users-in-your-directory"></a>适用于目录中的用户

如果希望允许目录中的用户请求此访问包，请执行以下步骤。 定义请求策略时，可以指定单个用户或更常见的用户组。 例如，你的组织可能已有一个组，例如 "**所有员工**"。  如果为可请求访问的用户在策略中添加该组，则该组的任何成员都可以请求访问。

1. 在“可以请求访问的用户”部分，单击“你目录中的用户”。

    如果选择此选项，则会显示新的选项，进一步优化目录中的哪些用户可以请求此访问包。

    ![访问包-请求-适用于目录中的用户](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. 选择以下选项之一：

    |  |  |
    | --- | --- |
    | **特定用户和组** | 如果你只希望你指定的目录中的用户和组能够请求此访问包，请选择此选项。 |
    | **所有成员（不包括来宾）** | 如果希望目录中的所有成员用户都能够请求此访问包，请选择此选项。 此选项不包括可能已邀请到目录的任何来宾用户。 |
    | **所有用户（包括来宾）** | 如果希望目录中的所有成员用户和来宾用户都能够请求此访问包，请选择此选项。 |

    来宾用户指的是已通过[AZURE AD B2B](../articles/active-directory/b2b/what-is-b2b.md)邀请加入你的目录的外部用户。 有关成员用户和来宾用户之间的差异的详细信息，请参阅[Azure Active Directory 中的默认用户权限是什么？](../articles/active-directory/fundamentals/users-default-permissions.md)。

1. 如果选择了 "**特定用户和组**"，请单击 "**添加用户和组**"。

1. 在 "选择用户和组" 窗格中，选择要添加的用户和组。

    ![访问包-请求-选择用户和组](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. 单击 "**选择**" 添加用户和组。

1. 向下跳到 "[审批](#approval)" 部分。

## <a name="for-users-not-in-your-directory"></a>对于不在你的目录中的用户

 **不在目录中的用户**是指位于另一个 Azure AD 目录或域中的用户。 这些用户可能尚未被邀请到你的目录。 必须将 Azure AD 目录配置为允许在**协作限制**中加入邀请。 有关详细信息，请参阅[启用 B2B 外部协作和管理可以邀请来宾的人员](../articles/active-directory/b2b/delegate-invitations.md)。

> [!NOTE]
> 将为你的请求已获批准或自动批准的用户创建来宾用户帐户。 将邀请来宾，但不会收到邀请电子邮件。 相反，在传递其访问包分配时，他们将收到一封电子邮件。 默认情况下，当来宾用户不再具有任何访问包分配时，因为他们的上次分配已过期或已被取消，则将阻止该来宾用户帐户登录并随后将其删除。 如果希望来宾用户无限期地保留在目录中，你可以更改你的授权管理配置的设置。 有关来宾用户对象的详细信息，请参阅[AZURE ACTIVE DIRECTORY B2B 协作用户的属性](../articles/active-directory/b2b/user-properties.md)。

如果要允许不在目录中的用户请求此访问包，请执行以下步骤：

1. 在 "**可请求访问的用户**" 部分中，单击 "**对于不在你的目录中的用户**"。

    如果选择此选项，将显示新选项。

    ![访问包-请求-对于不在你的目录中的用户](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. 选择以下选项之一：

    |  |  |
    | --- | --- |
    | **特定连接的组织** | 如果要从管理员之前添加的组织列表中进行选择，请选择此选项。 所选组织中的所有用户都可以请求此访问包。 |
    | **所有连接的组织** | 如果所有连接的组织中的所有用户都可以请求此访问包，请选择此选项。 |
    | **所有用户（所有连接的组织 + 任何新的外部用户）** | 如果所有连接的组织中的所有用户都可以请求此访问包，并且 B2B 允许或拒绝列表设置应优先于任何新的外部用户，请选择此选项。 |

    连接的组织是您与之有关系的外部 Azure AD 目录或域。

1. 如果选择了 "**特定连接的组织**"，请单击 "**添加目录**" 以从管理员之前添加的已连接组织列表中进行选择。

1. 键入名称或域名以搜索先前连接的组织。

    ![访问包-请求-选择目录](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    如果你想要与之合作的组织不在列表中，则可以要求管理员将其添加为连接的组织。 有关详细信息，请参阅[添加连接的组织](../articles/active-directory/governance/entitlement-management-organization.md)。

1. 选择所有连接的组织后，单击 "**选择**"。

    > [!NOTE]
    > 所选连接的组织中的所有用户都能够请求此访问包。 这包括与组织关联的所有子域 Azure AD 中的用户，除非这些域被 Azure B2B 允许或拒绝列表阻止。 有关详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](../articles/active-directory/b2b/allow-deny-list.md)。

1. 向下跳到 "[审批](#approval)" 部分。

## <a name="none-administrator-direct-assignments-only"></a>无（仅限管理员直接分配）

如果要跳过访问请求并允许管理员直接将特定用户分配到此访问包，请执行以下步骤。 用户无需请求访问包。 你仍可以设置生命周期设置，但没有请求设置。

1. 在 "**可请求访问的用户**" 部分中，单击 "**无（仅限管理员直接分配**"。

    ![访问包-请求-仅限管理员直接分配](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    创建访问包后，可以直接将特定的内部和外部用户分配到访问包。 如果指定外部用户，则将在目录中创建来宾用户帐户。 有关直接分配用户的信息，请参阅[查看、添加和删除访问包的分配](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)。

1. 向下跳到 "[启用请求](#enable-requests)" 部分。

## <a name="approval"></a>审批

在 "审批" 部分中，指定在用户请求此访问包时是否需要审批。 审批设置的工作方式如下：

- 只有一个选定的审批者或备用审批者需要批准单个阶段批准请求。 
- 每个阶段中只有一个选定的审批者需要批准两阶段审批的请求。
- 审批者可以是经理、内部主办方或外部赞助者，具体取决于策略管理访问的人员。
- 单个或两个阶段的审批不需要审批每个选定的审批者。
- 批准决策基于每个审批者首先查看请求。

有关如何向请求策略添加审批者的演示，请观看以下视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

有关如何向请求策略添加多阶段批准的演示，请观看以下视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

按照以下步骤为访问包的请求指定批准设置：

1. 若要要求从所选用户批准请求，请将 "**需要审批**" 切换设置为 **"是"** 。 或者，若要自动批准请求，请将切换设置为 "**否**"。

1. 若要要求用户提供理由来请求访问包，请将 "**需要请求者对齐**方式" 切换为 **"是"** 。
    
1. 现在，确定请求是需要单一还是2阶段批准。 设置单阶段批准切换为**1**的**阶段数**，或将切换到**2**阶段批准。

    ![访问包-请求-批准设置](./media/active-directory-entitlement-management-request-policy/approval.png)

选择所需的阶段数后，请使用以下步骤添加审批者： 

### <a name="single-stage-approval"></a>单阶段审批

1. 添加**第一个审批者**：
    
    如果策略设置为在目录中管理用户的访问权限，则可以选择 "**经理" 作为审批者**。 或者，通过在下拉菜单中选择 "选择特定审批者" 后单击 "**添加审批者**" 来添加特定的用户。
    
    ![访问包-请求-适用于目录优先审批者的用户](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    如果将此策略设置为 "控制用户不在你的目录中的访问权限"，则可以选择 "**外部赞助商**" 或 "**内部赞助**者"。 或者，通过在 "选择特定审批者" 下单击 "**添加审批者**" 或组来添加特定用户。
    
    ![访问包-请求-用户退出目录优先审批者](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. 如果选择了 "**管理器**" 作为首个审批者，请单击 "**添加回退**" 以选择目录中的一个或多个用户或组作为备用审批者。 如果授权管理找不到请求访问的用户的经理，则后备审批者将收到请求。

    使用**管理器**属性可通过授权管理找到管理器。 该属性在 Azure AD 的用户配置文件中。 有关详细信息，请参阅[使用 Azure Active Directory 添加或更新用户的配置文件信息](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)。

1. 如果选择了 "**选择特定审批者**"，请单击 "**添加审批**者"，选择要作为审批者的目录中的一个或多个用户或组。

1. 在 "**决定必须在多少天内进行决定**" 下的框中，指定审批者必须查看此访问包的请求的天数。

    如果在此时间段内未批准请求，则会自动拒绝该请求。 用户需要提交访问包的另一个请求。

1. 若要要求审批者为其决策提供理由，请将 "需要审批者理由" 设置为 **"是"** 。

    此理由对其他审批者和请求者可见。

### <a name="2-stage-approval-preview"></a>2阶段批准（预览）

如果选择了两阶段审批，则需要添加第二个审批者。

1. 添加**第二个审批者**： 
    
    如果用户位于你的目录中，请在 "选择特定审批者" 下单击 "**添加审批者**"，将特定用户添加为第二个审批者。

    ![访问包-请求-用户在目录中的第二个审批者](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    如果用户不在你的目录中，请选择 "**内部赞助**者" 或 "**外部赞助商**" 作为第二个审批者 选择审批者后，添加后备审批者。

    ![访问包-请求-用户退出目录-第二个审批者](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. 指定第二个审批者必须在 "**决定**" 下的框中批准请求的天数。 

1. 将 "需要审批者理由" 切换到 **"是" 或 "** **否**"。

### <a name="alternate-approvers"></a>备用审批者

可以指定备用审批者，类似于指定可批准请求的第一个和第二个审批者。 具有备用审批者将帮助确保请求在过期之前批准或拒绝（超时）。 可以为第一个审批者和第二个审批者列出备用审批者。 

通过指定备用审批者，如果第一个或第二个审批者无法批准或拒绝该请求，则会根据你在策略设置期间指定的转发计划，将挂起的请求转发给备用审批者。 他们会收到一封电子邮件，用于批准或拒绝挂起的请求。

将请求转发到备用审批者后，第一个或第二个审批者仍可批准或拒绝该请求。 备用审批者可以使用相同的访问站点来批准或拒绝挂起的请求。

我们可以列出要作为审批者和备用审批者的人员或人员组。 请确保将不同的人员组列出为第一、第二和备用审批者。
例如，如果你将 Alice 和 Bob 列为首个审批者，请将 Carol 和 Dave 作为备用审批者列出。 使用以下步骤将备用审批者添加到 access 包：

1. 在第一个审批者和/或第二个审批者下，单击 "**显示高级请求设置**"。

    ![访问包-策略-显示高级请求设置](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. **如果未采取任何操作，请转发到备用审批者，则**设置为 **"是"** 。

1. 单击 "**添加备用审批者**"，然后从列表中选择备用审批者。

    ![访问包-策略-添加备用审批者](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. 在 "开始**多少天后转发到备用审批者**" 框中，放入审批者批准或拒绝请求所需的天数。 如果在请求持续时间之前没有审批者批准或拒绝请求，则请求将过期（超时），用户必须提交访问包的另一个请求。 

    请求持续时间达到半期后，只能将请求转发给备用审批者。 在此示例中，请求的持续时间为14天。 因此，请求持续时间为第7天的半期。 因此，不能在早于8日之前转发该请求。 此外，请求不能在请求持续时间的最后一天转发。 因此，在此示例中，可以转发的最新请求为第13天。

## <a name="enable-requests"></a>启用请求

1. 如果希望访问包立即可供请求策略中的用户使用，请将 "启用" 切换切换为 **"是"** 。

    在完成创建访问包后，你始终可以启用它。

    如果选择了 "**无" （仅限管理员直接分配）** ，并将 "启用" 设置为 "**否**"，则管理员无法直接分配此访问包。

    ![访问包-策略-启用策略设置](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. 单击 **“下一步”** 。

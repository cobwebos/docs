---
title: include 文件
description: include 文件
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 7fd716be397d9ef6b9d6132cd4470f653f3cea0f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655944"
---
## <a name="for-users-in-your-directory"></a>适用于目录中的用户

如果要允许目录中的用户能够请求此访问包，请按照以下步骤操作。 定义请求策略时，可以指定单个用户，或者更常见的用户组。 例如，组织可能已经有一个组（例如“所有员工”）。****  如果将该组添加到可以请求访问权限的用户的策略中，则该组的任何成员都可以请求访问权限。

1. 在“可以请求访问的用户”部分，单击“你目录中的用户”。********

    选择此选项时，将显示新选项以进一步优化目录中谁可以请求此访问包。

    ![访问包 - 请求 - 适用于目录中的用户](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. 选择以下选项之一：

    |  |  |
    | --- | --- |
    | **特定用户和组** | 如果仅希望目录中的用户和组能够请求此访问包，请选择此选项。 |
    | **所有成员（不包括客人）** | 如果希望目录中的所有成员用户能够请求此访问包，请选择此选项。 此选项不包括您可能邀请到目录中的任何来宾用户。 |
    | **所有用户（包括客人）** | 如果希望目录中的所有成员用户和来宾用户能够请求此访问包，请选择此选项。 |

    来宾用户是指已使用[Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md)邀请到目录中的外部用户。 有关成员用户和来宾用户之间的差异的详细信息，请参阅[Azure 活动目录中的默认用户权限是什么？](../articles/active-directory/fundamentals/users-default-permissions.md)

1. 如果选择 **"特定用户和组**"，请单击"**添加用户和组**"。

1. 在“选择用户和组”窗格中，选择要添加的用户和组。

    ![访问包 - 请求 - 选择用户和组](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. 单击“选择”以添加用户和组。****

1. 向下跳到["审批"](#approval)部分。

## <a name="for-users-not-in-your-directory"></a>适用于不在目录中的用户

 **不在目录中的用户**是指位于其他 Azure AD 目录或域中的用户。 这些用户可能尚未被邀请到您的目录中。 Azure AD 目录必须配置为允许**协作限制**中的邀请。 有关详细信息，请参阅启用[B2B 外部协作并管理谁可以邀请来宾](../articles/active-directory/b2b/delegate-invitations.md)。

> [!NOTE]
> 将为不是目录中的其请求已审批或自动审批的用户创建来宾用户帐户。 将邀请来宾，但他们不会收到邀请电子邮件。 传递其访问包分配时，他们将收到电子邮件。 默认情况下，当来宾用户不再有任何访问包分配时（因为他们的上次分配已过期或已取消），将会阻止该来宾用户帐户登录并随后将其删除。 如果希望无限期地在目录中保留来宾用户（即使他们没有任何访问包分配），可以更改权利管理配置的设置。 有关来宾用户对象的详细信息，请参阅 Azure[活动目录 B2B 协作用户的属性](../articles/active-directory/b2b/user-properties.md)。

如果要允许目录中未的用户请求此访问包，请按照以下步骤操作：

1. 在 **"可以请求访问的用户"** 部分中，单击"**对于不在目录中的用户**"。

    选择此选项时，将显示新选项。

    ![访问包 - 请求 - 对于不在目录中的用户](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. 选择以下选项之一：

    |  |  |
    | --- | --- |
    | **特定互联组织** | 如果要从管理员以前添加的组织列表中选择，请选择此选项。 所选组织的所有用户都可以请求此访问包。 |
    | **所有互联组织** | 如果所有连接组织的所有用户都可以请求此访问包，请选择此选项。 |
    | **所有用户（所有连接的组织 + 任何新的外部用户）** | 如果所有已连接组织的所有用户都可以请求此访问包，并且 B2B 允许或拒绝列表设置应优先于任何新的外部用户，请选择此选项。 |

    已连接的组织是您与其有关系的外部 Azure AD 目录或域。

1. 如果选择 **"特定已连接的组织**"，请单击"**添加目录**"以从管理员以前添加的连接组织列表中选择。

1. 键入名称或域名以搜索以前连接的组织。

    ![访问包 - 请求 - 选择目录](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    如果要协作的组织不在列表中，可以要求管理员将其添加为已连接的组织。 有关详细信息，请参阅[添加已连接的组织](../articles/active-directory/governance/entitlement-management-organization.md)。

1. 选择所有已连接的组织后，单击"**选择**"。

    > [!NOTE]
    > 所选连接组织的所有用户将能够请求此访问包。 这包括 Azure AD 中来自与组织关联的所有子域的用户，除非 Azure B2B 允许或拒绝列表阻止这些域。 有关详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](../articles/active-directory/b2b/allow-deny-list.md)。

1. 向下跳到["审批"](#approval)部分。

## <a name="none-administrator-direct-assignments-only"></a>无(仅限管理员直接分配)

如果要绕过访问请求并允许管理员直接将特定用户分配给此访问包，请按照以下步骤操作。 用户无需请求访问包。 您仍然可以设置生命周期设置，但没有请求设置。

1. 在 **"可以请求访问的用户**"部分中，单击"**无"（管理员直接分配仅**。

    ![访问包 - 请求 - 无管理员直接分配](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    创建访问包后，可以直接将特定的内部和外部用户分配到该访问包。 如果指定外部用户，将在目录中创建来宾用户帐户。 有关直接分配用户的信息，请参阅[查看、添加和删除访问包的分配](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)。

1. 向下跳到[启用请求](#enable-requests)部分。

## <a name="approval"></a>审批

在"审批"部分中，您可以指定用户请求此访问包时是否需要批准。 审批设置的工作方式如下：

- 只有一个选定的审批人或回退审批者需要批准单阶段审批请求。 
- 每个阶段的选定审批者中只有一个需要批准两阶段审批请求。
- 审批人可以是经理、内部发起人或外部发起人，具体取决于政策管理访问的人员。
- 单阶段或两阶段审批不需要每个选定的审批人的批准。
- 审批决定以第一个评审请求的审批者为准。

有关如何向请求策略添加审批人演示，请观看以下视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

有关如何向请求策略添加多阶段审批的演示，请观看以下视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

按照以下步骤指定访问包请求的审批设置：

1. 若要要求对所选用户发起的请求进行审批，请将“需要审批”切换开关设置为“是”。******** 或者，要自动批准请求，请将切换设置为 **"否**"。

1. 要要求用户提供请求访问包的理由，请将 **"要求请求者理由**"切换为 **"是**"。
    
1. 现在确定请求是否需要单阶段或 2 阶段批准。 将"**单个阶段审批的多少个阶段**切换为**1"，** 或将切换设置为**2**进行 2 阶段审批。

    ![访问包 - 请求 - 批准设置](./media/active-directory-entitlement-management-request-policy/approval.png)

在选择需要多少阶段后，使用以下步骤添加审批者： 

### <a name="single-stage-approval"></a>单阶段审批

1. 添加**第一个审批者**：
    
    如果策略设置为控制目录中用户的访问权限，则可以选择**Manager 作为审批者**。 或者，在选择下拉菜单中选择特定审批人后，单击 **"添加审批者**"来添加特定用户。
    
    ![访问包 - 请求 - 对于目录中的用户 - 第一个审批者](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    如果此策略设置为管理不在目录中的用户的访问，则可以选择**外部赞助商**或**内部赞助商**。 或者，通过单击"选择特定审批**人"下的"添加审批者**"或"组"来添加特定用户。
    
    ![访问包 - 请求 - 对于目录外的用户 - 第一个审批者](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. 如果选择**Manager**作为第一个审批者，请单击"**添加回退**"以选择目录中的一个或多个用户或组作为回退审批人。 如果授权管理找不到请求访问的用户的管理器，则回退审批者将收到请求。

    通过使用 Manager 属性的授权管理找到**管理器**。 该属性位于 Azure AD 中的用户的配置文件中。 有关详细信息，请参阅使用[Azure 活动目录添加或更新用户的配置文件信息](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)。

1. 如果选择 **"选择特定审批者**"，请单击"**添加审批者**"以选择目录中的一个或多个用户或组作为审批者。

1. 在 **"决定"下的框中，必须在多少天内做出？，** 指定审批人必须审核此访问包请求的天数。

    如果请求在此时间段内未获得批准，则将自动拒绝。 用户必须提交访问包的另一个请求。

1. 要要求审批人为其决策提供理由，请将"要求批准人的理由"设置为 **"是**"。

    其他审批人和请求者可以看到理由。

### <a name="2-stage-approval"></a>2 阶段批准

如果选择了 2 阶段批准，则需要添加第二个审批人。

1. 添加第**二个审批者**： 
    
    如果用户位于目录中，请单击"选择特定审批人"下的 **"添加审批者**"，将特定用户添加为第二个审批者。

    ![访问包 - 请求 - 对于目录中的用户 - 第二个审批者](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    如果用户不在目录中，请选择 **"内部发起人**"或 **"外部发起人**"作为第二个审批者。 选择审批人后，添加回退审批人。

    ![访问包 - 请求 - 对于目录外的用户 - 第二个审批者](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. 指定第二个审批人必须在"决定"框中批准请求的天数，**必须在多少天内做出。。** 

1. 将"需要审批人理由"切换为 **"是**"或 **"否**"。

### <a name="alternate-approvers"></a>候补审批人

您可以指定备用审批人，类似于指定可以批准请求的第一个和第二个审批人。 拥有备用审批人有助于确保请求在过期（超时）之前获得批准或拒绝。 您可以列出候补审批人、第一个审批人和第二个审批人，以便进行两阶段审批。 

通过指定备用审批人，如果第一个或第二个审批人无法批准或拒绝请求，则根据您在策略设置期间指定的转发计划，将挂起的请求转发给备用审批人。 他们收到一封电子邮件以批准或拒绝挂起的请求。

将请求转发给备用审批人后，第一个或第二个审批人仍然可以批准或拒绝请求。 备用审批人使用相同的"我的访问"网站来批准或拒绝挂起的请求。

我们可以列出作为审批人和候补审批人的人或人组。 请确保列出不同的人员集，作为第一、第二和备用审批者。
例如，如果您将 Alice 和 Bob 列为第一审批人，则将卡罗尔和 Dave 列为候补审批人。 使用以下步骤将备用审批人添加到访问包：

1. 在第一个审批者、第二个审批者或两者下，单击"**显示高级请求设置**"。

    ![访问包 - 策略 - 显示高级请求设置](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. 设置**如果没有执行任何操作，则转发给备用审批人？** 切换为 **"是**"。

1. 单击 **"添加备用审批人**"并从列表中选择备用审批人。

    ![访问包 - 策略 - 添加备用审批人](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. 在 **"转发到备用审批人"中，在多少天之后**框中，放入审批人必须批准或拒绝请求的天数。 如果在请求持续时间之前没有审批或拒绝请求，则请求过期（超时），并且用户必须提交访问包的另一个请求。 

    请求只能在请求期限达到半衰期后一天转发给备用审批人。 在此示例中，请求的持续时间为 14 天。 因此，请求持续时间在第 7 天达到半衰期。 因此，请求不能早于第 8 天转发。 此外，请求无法在请求持续时间的最后一天转发。 因此，在此示例中，可以转发请求的最新请求是第 13 天。

## <a name="enable-requests"></a>启用请求

1. 如果希望访问包立即可供请求策略中的用户使用，请将"启用"切换移动到 **"是**"。

    创建完访问包后，将来始终可以启用该策略。

    如果选择了 **"无"（仅限管理员直接分配），** 并且将启用设置为 **"否**"，则管理员无法直接分配此访问包。

    ![访问包 - 策略 - 启用策略设置](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. 单击“下一步”。 

---
title: 包含文件
description: 包含文件
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 12c9b1226e3ba928a4062049c7839d4e46ef727d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025105"
---
## <a name="for-users-in-your-directory"></a>适用于目录中的用户

如果你希望目录中的用户可请求此访问包，请执行以下步骤。 定义请求策略时，可以指定单个用户，也可以指定用户组（通常做法）。 例如，组织可能已经有一个组（例如“所有员工”）。****  如果将该组添加到可以请求访问权限的用户的策略中，则该组的任何成员都可以请求访问权限。

1. 在“可以请求访问的用户”部分，单击“你目录中的用户”。********

    选择此选项后，会出现新的选项以进一步优化目录中哪些用户可以请求此访问包。

    ![访问包 - 请求 - 你目录中的用户](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. 选择以下选项之一：

    |  |  |
    | --- | --- |
    | **特定用户和组** | 如果只希望目录中的指定用户和组可以请求此访问包，请选择此选项。 |
    | **所有成员（不包括来宾）** | 如果希望目录中的所有成员用户都可以请求此访问包，请选择此选项。 此选项不包括你可能已邀请到目录中的任何来宾用户。 |
    | **所有用户（包括来宾）** | 如果希望目录中的所有成员用户和来宾用户都可以请求此访问包，请选择此选项。 |

    来宾用户是指通过 [Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md) 邀请到目录中的外部用户。 有关成员用户和来宾用户之间差异的详细信息，请参阅 [Azure Active Directory 中的默认用户权限是什么？](../articles/active-directory/fundamentals/users-default-permissions.md)。

1. 如果已选择“特定用户和组”，请单击“添加用户和组” 。

1. 在“选择用户和组”窗格中，选择要添加的用户和组。

    ![访问包 - 请求 - 选择用户和组](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. 单击“选择”以添加用户和组。****

1. 跳到[审批](#approval)部分。

## <a name="for-users-not-in-your-directory"></a>适用于不在目录中的用户

 “不在目录中的用户”是指位于其他 Azure AD 目录或域中的用户。 这些用户可能尚未被邀请到目录中。 Azure AD 目录必须配置为允许”协作限制”中的邀请。 有关详细信息，请参阅[启用 B2B 外部协作并管理谁可以邀请来宾](../articles/active-directory/b2b/delegate-invitations.md)。

> [!NOTE]
> 将为不是目录中的其请求已审批或自动审批的用户创建来宾用户帐户。 将邀请来宾，但他们不会收到邀请电子邮件。 传递其访问包分配时，他们将收到电子邮件。 默认情况下，当来宾用户不再有任何访问包分配时（因为他们的上次分配已过期或已取消），将会阻止该来宾用户帐户登录并随后将其删除。 如果希望无限期地在目录中保留来宾用户（即使他们没有任何访问包分配），可以更改权利管理配置的设置。 有关来宾用户对象的详细信息，请参阅 [Azure Active Directory B2B 协作用户的属性](../articles/active-directory/b2b/user-properties.md)。

如果要允许不在目录中的用户请求此访问包，请执行以下步骤：

1. 在“可以请求访问的用户”部分，单击“不在目录中的用户” 。

    选择此选项时，将显示新选项。

    ![访问包 - 请求 - 不在目录中的用户](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. 选择以下选项之一：

    |  |  |
    | --- | --- |
    | **特定的已连接的组织** | 如果要从管理员之前添加的组织列表中选择，请选择此选项。 来自选定组织的所有用户都可以请求此访问包。 |
    | **所有已连接的组织** | 如果所有已连接的组织的用户都可以请求此访问包，请选择此选项。 |
    | **所有用户（所有已连接的组织 + 任何新外部用户）** | 如果来自所有已连接的组织的所有用户都可以请求此访问包，并且 B2B 允许或拒绝列表设置对于任何新的外部用户都应优先，请选择此选项。 |

    已连接的组织是与你有关系的外部 Azure AD 目录或域。

1. 如果选择了“特定连接的组织”，请单击“添加目录”，从管理员之前添加的已连接的组织列表中进行选择 。

1. 键入要搜索的之前已连接的组织的名称或域名。

    ![访问包 - 请求 - 选择目录](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    如果要与之协作的组织不在列表中，则可以要求管理员将其添加为已连接的组织。 有关详细信息，请参阅[添加已连接的组织](../articles/active-directory/governance/entitlement-management-organization.md)。

1. 选择所有已连接的组织后，单击“选择”。

    > [!NOTE]
    > 来自选定已连接的组织的所有用户都将可以请求此访问包。 这包括来自与组织关联的所有子域的 Azure AD 中的用户，除非这些域被 Azure B2B 允许或拒绝列表阻止。 有关详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](../articles/active-directory/b2b/allow-deny-list.md)。

1. 跳到[审批](#approval)部分。

## <a name="none-administrator-direct-assignments-only"></a>无(仅限管理员直接分配)

如果希望绕过访问请求，并允许管理员直接将特定用户分配到访问包，请执行这些步骤。 用户无需请求访问包。 仍可以设置生命周期设置，但没有请求设置。

1. 在“可以请求访问权限的用户”部分，单击“无（仅限管理员直接分配）” 。

    ![访问包 - 请求 - 无（仅限管理员直接分配）](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    创建访问包后，可以直接将特定的内部和外部用户分配到该访问包。 如果指定外部用户，将在目录中创建来宾用户帐户。 有关直接分配用户的详细信息，请参阅[查看、添加和删除访问包的分配](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)。

1. 跳到[启用请求](#enable-requests)部分。

## <a name="approval"></a>审批

在“审批”部分中，指定用户请求此访问包时是否需要审批。 审批设置的工作方式如下：

- 只有一个选定审批者或后备审批者需要批准单阶段审批的请求。 
- 每个阶段中只有一个选定审批者需要批准两阶段审批的请求。
- 审批者可以是管理员、内部发起人或外部发起人，具体取决于策略管理谁的访问权限。
- 单阶段或两阶段审批不需要每个选定审批者的批准。
- 审批决定以第一个评审请求的审批者为准。

有关如何向请求策略添加审批者的演示，请观看以下视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

有关如何向请求策略添加多阶段审批的演示，请观看以下视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

请按照以下步骤指定请求访问包的审批设置：

1. 若要要求对所选用户发起的请求进行审批，请将“需要审批”切换开关设置为“是”。******** 或者，若要自动审批请求，请将切换开关设置为“否”。

1. 如果需要用户提供对请求访问包的论证，请将“需要请求者论证”切换开关设置为“是”。 。
    
1. 现在确定请求是否需要单阶段或两阶段审批。 对于单阶段审批，将”阶段数“切换开关设置为“1”，或者对于两阶段审批，将切换开关设置为“2”  。

    ![访问包 - 请求 - 审批设置](./media/active-directory-entitlement-management-request-policy/approval.png)

选择所需的阶段数后，使用以下步骤添加审批者： 

### <a name="single-stage-approval"></a>单阶段审批

1. 添加“第一位审批者”：
    
    如果策略设置为管理目录中的用户访问，可以选择“管理员充当审批者”。 或者，在下拉菜单中选择“选择特定审批者”后，单击“添加审批者”，以添加特定用户。
    
    ![访问包 - 请求 - 目录中的用户 - 第一位审批者](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    如果将此策略设置为管理不在目录中的用户的访问，则可以选择“外部发起人”或“内部发起人” 。 或者，通过单击“选择特定审批者”下的“添加审批者”或组来添加特定用户。
    
    ![访问包 - 请求 - 目录外的用户 - 第一位审批者](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. 如果你选择了“管理员”作为第一位审批者，请单击“添加后备审批者”，以选择目录中的一个或多个用户或组作为后备审批者 。 如果权利管理找不到请求访问权限的用户的管理员，后备审批者将收到请求。

    权利管理使用“管理员”属性找到管理员。 该属性位于 Azure AD 中的用户配置文件中。 有关详细信息，请参阅[使用 Azure Active Directory 添加或更新用户的配置文件信息](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)。

1. 如果选择了“选择特定审批者”，请单击“添加审批者”以选择目录中的一个或多个用户或组作为审批者 。

1. 在“必须在多少天内作出决定？”框下，指定审批者审阅对此访问包的请求的允许天数。

    如果请求在这段时间内未获批准，将自动被拒绝。 用户必须再提交一个访问包的请求。

1. 如果需要审批者提供其决策论证，请将“需要审批者论证”设置为“是”。

    其他审批者和请求者都可以看到该论证。

### <a name="2-stage-approval"></a>两阶段审批

如果选择了两阶段审批，则需要添加第二位审批者。

1. 添加“第二位审批者”： 
    
    如果用户在目录中，可以通过单击“选择特定审批者”下的“添加审批者”添加特定用户作为第二位审批者。

    ![访问包 - 请求 - 目录中的用户 - 第二位审批者](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    如果用户不在目录中，请选择“内部发起人”或“外部发起人”作为第二位审批者 。 选择审批者后，添加后备审批者。

    ![访问包 - 请求 - 目录外的用户 - 第二位审批者](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. 在“必须在多少天内做出决策？”下的框中指定第二位审批者审批请求的允许天数。 

1. 将“需要审批者论证”切换开关设置为“是”或“否” 。

### <a name="alternate-approvers"></a>后备审批者

可以指定后备审批者，类似于指定可以审批请求的第一位和第二位审批者。 指定后备审批者将有助于确保请求在到期（超时）之前被批准或拒绝。 针对两阶段审批，可以为后备审批者列出第一位审批者和第二位审批者。 

指定后备审批者后，在第一位或第二位审批者无法批准或拒绝请求的情况下，待处理的请求将根据你在策略设置期间指定的转发计划转发给后备审批者。 他们会收到一封批准或拒绝待处理请求的电子邮件。

请求被转发给后备审批者后，第一位或第二位审批者仍可批准或拒绝该请求。 后备审批者使用同一个“我的访问权限”网站来批准或拒绝待处理的请求。

我们可以列出要成为审批者和后备审批者的人员或组。 请确保列出不同的人员集作为第一位、第二位和后备审批者。
例如，如果将 Alice 和 Bob 列为第一位审批者，则将 Carol 和 Dave 列为后备审批者。 通过以下步骤将后备审批者添加到访问包中：

1. 在“第一位审批者”和/或“第二位审批者”下，单击“显示高级请求设置”。

    ![访问包 - 策略 - 显示高级请求设置](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. 将“若没有采取任何行动，转发给后备审批者们？”切换开关设置为“是” 。

1. 单击“添加后备审批者”，然后从列表中选择后备审批者。

    ![访问包 - 策略 - 添加后备审批者](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. 在“多少天后转发给后备审批者？”框中，输入审批者批准或拒绝请求的允许天数。 如果在请求持续期间没有审批者批准或拒绝请求，则请求将过期（超时），用户必须再提交一个访问包请求。 

    只有在请求持续时间已过一半后，请求才会被转发给后备审批者，主审批者的决策必须在至少 4 天后超时。 如果请求超时小于或等于 3，则没有足够的时间将请求转发给后备审批者。 在本例中，请求的持续时间为 14 天。 因此，请求持续时间在第 7 天已达到一半。 所以请求不能早于第 8 天转发。 此外，请求不能在请求持续时间的最后一天转发。 因此，在本例中，可以转发请求的最晚时间是第 13 天。

## <a name="enable-requests"></a>启用请求

1. 如果希望访问包立即可供请求策略中的用户使用以进行请求，请将“启用”切换开关移动到“是”。

    创建完访问包后，将来始终可以启用该策略。

    如果选择了“无（仅限管理员直接分配）”，并且将“启用”设置为“否”，则管理员无法直接分配此访问包 。

    ![访问包 - 策略 - 启用策略设置](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. 单击“下一步”  。

## <a name="add-requestor-information-preview-to-an-access-package"></a>将请求者信息 (预览版添加到 access 包) 

1. 请参阅 " **请求者信息** " 选项卡，并单击 " **问题** " 子选项卡。
 
1. 在 " **问题** " 框中键入要向请求者请求的内容，也称为 "显示字符串"。

    ![访问包-策略-启用请求程序信息设置](./media/active-directory-entitlement-management-request-policy/add-requestor-info-question.png)

1. 如果要添加自己的本地化选项，请单击 " **添加本地化**"。
    1. 进入 " **添加本地化 for 试题** " 窗格后，选择要在其中本地化问题的语言的 **语言代码** 。
    1. 在配置的语言中，在 " **本地化" 文本框** 中键入问题。
    1. 添加完所需的所有本地化后，单击 " **保存**"。

    ![访问包-策略-配置本地化文本](./media/active-directory-entitlement-management-request-policy/add-localization-question.png)

1. 选择要在其中回答请求方的 **答案格式** 。 答案格式包括： *短文本*、 *多选*和 *长文本*。
 
    ![访问包-策略-选择 "查看和编辑多个选择答案格式"](./media/active-directory-entitlement-management-request-policy/answer-format-view-edit.png)
 
1. 如果选择多个选项，请单击 " **视图" 和 "编辑** " 按钮以配置答案选项。
    1. 选择 "查看和编辑" 后，将打开 " **查看/编辑问题** " 窗格。
    1. 键入 **回答 "答案值** " 框中的问题时要为请求者指定的响应选项。
    1. 键入所需的任意多个响应，并单击 " **保存**"。
    
    ![访问包-策略-输入多个选择选项](./media/active-directory-entitlement-management-request-policy/answer-multiple-choice.png)
  
1. 若要要求请求者在请求访问访问包时回答此问题，请单击 " **必需**" 下的复选框。

1. 单击“下一步”

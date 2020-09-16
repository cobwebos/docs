---
title: 创建对组和应用程序的访问评审 - Azure AD
description: 了解如何在 Azure Active Directory 访问评审中创建对组成员或应用程序访问的访问评审。
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/15/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02d1c40c26dd6b6992d8df85a986b4157a22226a
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602925"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 访问评审中创建对组和应用程序的访问评审

员工和来宾对组和应用程序的访问权限会不断变化。 为了降低与过期访问权限分配相关的风险，管理员可以使用 Azure Active Directory (Azure AD) 针对组成员或应用程序访问权限创建访问评审。 如果需要定期评审访问权限，则还可以创建定期访问评审。 有关这些方案的详细信息，请参阅[管理用户访问权限](manage-user-access-with-access-reviews.md)和[管理来宾访问权限](manage-guest-access-with-access-reviews.md)。

你可以观看有关如何启用访问评审的快速视频：

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

本文介绍如何针对组成员或应用程序访问权限创建一个或多个访问评审。

## <a name="prerequisites"></a>必备条件

- Azure AD Premium P2
- 全局管理员或用户管理员

有关详细信息，请参阅[许可证要求](access-reviews-overview.md#license-requirements)。

## <a name="create-one-or-more-access-reviews"></a>创建一个或多个访问评审

1. 登录到 Azure 门户并打开[“标识治理”页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 在左侧菜单中，单击“访问评审”。 

1. 单击“新建访问评审”创建新的访问评审。 

    ![标识治理中的“访问评审”窗格](./media/create-access-review/access-reviews.png)

1. 命名访问评审。 可选择为评审提供说明。 名称和说明向评审者显示。

    ![创建访问评审 - 评审名称和说明](./media/create-access-review/name-description.png)

1. 设置“开始日期”。  默认情况下，访问评审只进行一次，从创建的时候开始，在一个月内结束。 可以更改开始和结束日期，使访问评审在将来的时间开始，并持续所需的天数。

    ![创建访问评审 - 开始和结束日期](./media/create-access-review/start-end-dates.png)

1. 若要让访问评审定期进行，请将“频率”设置从“一次”更改为“每周”、“每月”、“每季”、“半年”或“每年”。       请使用“持续时间”滑块或文本框来定义定期进行的一系列评审每次的运行天数（可供审阅者输入）。  例如，每月评审的最长持续时间可以设置为 27 天，以免评审时间重叠。

1. 使用“结束”设置指定如何结束定期访问评审系列。  序列可以通过以下三种方式结束： 
    1. 它连续运行来无限期地开始审阅
    1. 直到特定日期，
    1. 直到完成了定义的次数为止。 
  
    你、其他用户管理员或其他全局管理员可以在创建后停止此系列，只需在“设置”中更改日期，然后此系列就会在该日期结束。 

1. 在“用户”部分，指定访问评审要应用到的用户。  访问评审的对象可以是组成员，或者是已分配到应用程序的用户。 可将访问评审的范围进一步限定为仅评审属于成员（或已分配到应用程序）的来宾用户，而不是评审属于成员或有权访问应用程序的所有用户。

    ![创建访问评审 - 用户](./media/create-access-review/users.png)

1. 在“组”部分选择一个或多个组，以便查看其成员身份。 

    > [!NOTE]
    > 选择多个组会创建多个访问评审。 例如，选择五个组会创建五个单独的访问评审。
    
    ![创建访问评审 - 选择组](./media/create-access-review/select-group.png)

1. 在“应用程序”部分  （如果已在步骤 8 中选择了“分配到应用程序”）  ，选择要评审对其的访问权限的应用程序。

    > [!NOTE]
    > 选择多个应用程序会创建多个访问评审。 例如，选择五个应用程序会创建五个单独的访问评审。
    
    ![创建访问评审 - 选择应用程序](./media/create-access-review/select-application.png)

1. 在“评审者”部分选择一人或多人来评审范围内的所有用户。  也可以选择让成员评审自己的访问权限。 如果资源是一个组，可以要求组的所有者进行评审。 还可以要求评审者在审批访问权限时提供原因。

    ![创建访问评审 - 评审者](./media/create-access-review/reviewers.png)

1. 在“计划”部分选择要使用的计划。  “默认计划”将始终存在  。

    ![创建访问评审 - 计划](./media/create-access-review/programs.png)

    可以通过将访问评审组织到程序中来简化其收集和跟踪。 可以将每个访问评审链接到一个计划。 然后，在为审核员准备报告时，可以将重点放在特定计划范围内的访问评审。 “全局管理员”、“用户管理员”、“安全管理员”或“安全读取者者”角色中的用户可以看到计划和访问评审结果。

    若要查看计划列表，请转到访问评审页，选择“计划”。  如果拥有全局管理员或用户管理员角色，则可以创建其他计划。 例如，可以选择针对每个符合性措施或业务目标创建一个计划。 如果不再需要某个程序，并且没有任何链接到它的控件，则可以将其删除。

### <a name="upon-completion-settings"></a>完成后的设置

1. 若要指定评审完成后发生的情况，请展开“完成后的设置”部分。 

    ![创建访问评审 -“完成后操作”设置](./media/create-access-review/upon-completion-settings-new.png)

2. 如果要自动删除拒绝用户的访问权限，请将 " **自动应用结果** " 设置为 "要 **启用**的资源"。 若要在评审完成后手动应用结果，请将开关设置为“禁用”。 

3. 使用 " **如果审阅者未响应** " 列表来指定审阅期限内审阅者未审阅的用户将发生的情况。 此设置不影响审阅者已手动评审的用户。 如果最终的审阅者决策是“拒绝”，则会删除用户的访问权限。

    - **不更改** - 将用户访问权限保持不变
    - **删除访问权限** - 删除用户的访问权限
    - **批准访问权限** - 批准用户的访问权限
    - **采用建议** - 根据系统的建议拒绝或批准用户的持续访问权限

    ![创建访问评审 - 高级设置](./media/create-access-review/advanced-settings-preview-new.png)

4.  (预览) 使用 "操作" 应用于 "拒绝的用户"，以指定在来宾用户被拒绝时所发生的情况。
    - **选项 1** 将删除被拒绝的用户对要查看的组或应用程序的访问权限，它们仍将能够登录到租户。 
    - **选项 2** 将阻止拒绝的用户登录到租户，无论他们是否有权访问其他资源。 如果出现错误，或者管理员决定重新启用一次访问，则可以在用户禁用后30天内执行此操作。 如果在禁用的用户上没有执行任何操作，则会从租户中删除这些用户。

若要详细了解删除不能再访问你组织中的资源的来宾用户的最佳做法，请阅读标题为[使用 Azure AD Identity Governance 查看和删除不再具有资源访问权限的外部用户。](access-reviews-external-users.md)

>[!NOTE]
> 适用于已拒绝用户的操作仅适用于你之前确定仅限来宾用户的评审 (请参阅 **创建一个或多个访问评审** 部分步骤 8) 

### <a name="advanced-settings"></a>高级设置

1. 若要指定其他设置，请展开“高级设置”部分。 

1. 将“显示建议”设置为“启用”，以基于用户的访问权限信息向评审者显示系统建议。  

1. 将“需要提供审批原因”设置为“启用”，以要求审阅者提供批准原因。  

1. 将“邮件通知”设置为“启用”，以便在访问评审开始时让 Azure AD 向评审者发送电子邮件通知，并在评审完成时向管理员发送电子邮件通知。  

1. 将“提醒”设置为“启用”，让 Azure AD 向尚未完成其审阅的审阅者发送访问评审正在进行的提醒。   

    >[!NOTE]
    > 默认情况下，Azure AD 自动在中途向还未作出回复的审阅者发送结束日期提醒

1.  (预览) 发送给审阅者的电子邮件的内容根据评审详细信息（如审阅名称、资源名称、截止日期等）自动生成。如果你需要一种方式来传达其他信息（如其他说明或联系人信息），则可以在 **审阅者电子邮件的其他内容** 中指定这些详细信息，该电子邮件将包含在发送给分配的审阅者的邀请和提醒电子邮件中。 下面突出显示的部分是将要显示此信息的位置。

    ![查看用户对组的访问权限](./media/create-access-review/review-users-access-group.png)

## <a name="start-the-access-review"></a>启动访问评审

指定访问评审的设置后，单击“启动”。**** 访问评审将显示在列表中，并带有其状态指示器。

![访问评审及其状态的列表](./media/create-access-review/access-reviews-list.png)

默认情况下，在评审开始后不久，Azure AD 会向评审者发送一封电子邮件。 如果选择不让 Azure AD 发送电子邮件，请务必通知评审者有一个访问评审任务等待他们完成。 可以向他们显示有关如何[评审对组或应用程序的访问权限](perform-access-review.md)的说明。 如果评审工作是让来宾评审他们自己的访问权限，则可以显示有关如何[评审自己对组或应用程序的访问权限](review-your-access.md)的说明。

如果将来宾指定为评审者，但他们尚未接受邀请，则他们不会收到来自访问评审的电子邮件，因为在评审前必须先接受邀请。

## <a name="access-review-status-table"></a>访问评审状态表

| 状态 | 定义 |
|--------|------------|
|NotStarted | 已创建评审，用户发现正在等待启动。 |
|正在初始化   | 正在进行用户发现，以标识属于评审的所有用户。 |
|正在启动 | 正在启动评审。 如果启用电子邮件通知，则会将电子邮件发送给评审者。 |
|正在进行 | 已开始评审。 如果启用电子邮件通知，则电子邮件已发送给评审者。 评审者可以在截止日期之前提交决定。 |
|正在完成 | 评审正在完成，电子邮件将发送到评审所有者。 |
|自动检查 | 评审处于系统评审阶段。 系统正在为未根据建议或预先配置的决定评审的用户记录决定。 |
|自动审阅 | 系统已为未评审的所有用户记录了决定。 如果启用了“自动应用”，则评审可以进入“正在应用”阶段了。 |
|正在应用 | 对于已批准的用户，访问权限不会更改。 |
|已应用 | 已经从源或目录删除拒绝的用户（如果有）。 |

## <a name="create-reviews-via-apis"></a>通过 API 创建评审

也可以使用 API 创建访问评审。 在 Azure 门户中管理组和应用程序用户的访问评审的方法也可以使用 Microsoft Graph API 来实现。 有关详细信息，请参阅 [Azure AD 访问评审 API 参考](/graph/api/resources/accessreviews-root?view=graph-rest-beta)。 有关代码示例，请参阅[通过 Microsoft Graph 检索 Azure AD 访问评审的示例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)。

## <a name="next-steps"></a>后续步骤

- [评审组或应用程序的访问权限](perform-access-review.md)
- [评审自己对组或应用程序的访问权限](review-your-access.md)
- [完成组或应用程序的访问评审](complete-access-review.md)
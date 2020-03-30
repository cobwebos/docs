---
title: 创建应用程序&组的访问审查 - Azure AD
description: 了解如何在 Azure Active Directory 访问评审中创建对组成员或应用程序访问的访问评审。
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3b8f2cf7db474f2a67ebaed818a0a440a195326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128839"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 访问审核中创建组和应用程序的访问审核

员工和来宾对组和应用程序的访问权限会不断变化。 为了降低与过期访问权限分配相关的风险，管理员可以使用 Azure Active Directory (Azure AD) 针对组成员或应用程序访问权限创建访问评审。 如果需要定期评审访问权限，则还可以创建定期访问评审。 有关这些方案的详细信息，请参阅[管理用户访问权限](manage-user-access-with-access-reviews.md)和[管理来宾访问权限](manage-guest-access-with-access-reviews.md)。

本文介绍如何针对组成员或应用程序访问权限创建一个或多个访问评审。

## <a name="prerequisites"></a>先决条件

- Azure AD Premium P2
- 全局管理员或用户管理员

有关详细信息，请参阅[许可证要求](access-reviews-overview.md#license-requirements)。

## <a name="create-one-or-more-access-reviews"></a>创建一个或多个访问评审

1. 登录到 Azure 门户并打开[“标识治理”页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 在左侧菜单中，单击“访问评审”。****

1. 单击“新建访问评审”创建新的访问评审。****

    ![标识治理中的“访问评审”窗格](./media/create-access-review/access-reviews.png)

1. 命名访问评审。 可选择为评审提供说明。 名称和说明向评审者显示。

    ![创建访问评审 - 评审名称和说明](./media/create-access-review/name-description.png)

1. 设置“开始日期”。**** 默认情况下，访问评审只进行一次，从创建的时候开始，在一个月内结束。 可以更改开始和结束日期，使访问评审在将来的时间开始，并持续所需的天数。

    ![创建访问评审 - 开始和结束日期](./media/create-access-review/start-end-dates.png)

1. 要使访问审核重复进行，将**频率**设置从 **"一次**"更改为 **"每周**、**每月**、**每季度**、**每半年**"或 **"每年**"。 请使用“持续时间”滑块或文本框来定义定期进行的一系列评审每次的运行天数（可供审阅者输入）。**** 例如，每月评审的最长持续时间可以设置为 27 天，以免评审时间重叠。

1. 使用“结束”设置指定如何结束定期访问评审系列。**** 系列的结束方式有三种：持续运行，无限期地开始评审；运行至指定日期；运行至已完成定义的评审数目。 你、其他用户管理员或其他全局管理员可以在创建后停止此系列，只需在“设置”中更改日期，然后此系列就会在该日期结束。****

1. 在“用户”部分，指定访问评审要应用到的用户。**** 访问评审的对象可以是组成员，或者是已分配到应用程序的用户。 可将访问评审的范围进一步限定为仅评审属于成员（或已分配到应用程序）的来宾用户，而不是评审属于成员或有权访问应用程序的所有用户。

    ![创建访问评审 - 用户](./media/create-access-review/users.png)

1. 在“组”部分选择一个或多个组，以便查看其成员身份。****

    > [!NOTE]
    > 选择多个组会创建多个访问评审。 例如，选择五个组会创建五个单独的访问评审。
    
    ![创建访问评审 - 选择组](./media/create-access-review/select-group.png)

1. 在“应用程序”部分****（如果已在步骤 8 中选择了“分配到应用程序”）****，选择要评审对其的访问权限的应用程序。

    > [!NOTE]
    > 选择多个应用程序会创建多个访问评审。 例如，选择五个应用程序会创建五个单独的访问评审。
    
    ![创建访问评审 - 选择应用程序](./media/create-access-review/select-application.png)

1. 在“评审者”部分选择一人或多人来评审范围内的所有用户。**** 也可以选择让成员评审自己的访问权限。 如果资源是一个组，可以要求组的所有者进行评审。 还可以要求评审者在审批访问权限时提供原因。

    ![创建访问评审 - 评审者](./media/create-access-review/reviewers.png)

1. 在“计划”部分选择要使用的计划。**** “默认计划”将始终存在****。

    ![创建访问评审 - 计划](./media/create-access-review/programs.png)

    可以将访问评审组织为计划，以便针对不同目的简化跟踪和收集访问评审的方式。 可以将每个访问评审链接到一个计划。 然后，在为审核员准备报告时，可以将重点放在特定计划范围内的访问评审。 “全局管理员”、“用户管理员”、“安全管理员”或“安全读取者者”角色中的用户可以看到计划和访问评审结果。

    若要查看计划列表，请转到访问评审页，选择“计划”****。 如果拥有全局管理员或用户管理员角色，则可以创建其他计划。 例如，可以选择针对每个符合性措施或业务目标创建一个计划。 如果不再需要某个计划，并且没有任何链接到它的控件，则可以将其删除。

### <a name="upon-completion-settings"></a>完成后的设置

1. 若要指定评审完成后发生的情况，请展开“完成后的设置”部分。****

    ![创建访问评审 -“完成后操作”设置](./media/create-access-review/upon-completion-settings.png)

1. 如果要自动删除，请对被拒绝的用户进行访问权限，将 **"自动将结果应用于资源****以启用**"。 若要在评审完成后手动应用结果，请将开关设置为“禁用”。****

1. 使用“如果审阅者未答复”列表指定对于审阅者在评审期限内未评审的用户要执行的操作。**** 此设置不影响审阅者已手动评审的用户。 如果最终的审阅者决策是“拒绝”，则会删除用户的访问权限。

    - **不更改** - 将用户访问权限保持不变
    - **删除访问权限** - 删除用户的访问权限
    - **批准访问权限** - 批准用户的访问权限
    - **采用建议** - 根据系统的建议拒绝或批准用户的持续访问权限

### <a name="advanced-settings"></a>高级设置

1. 若要指定其他设置，请展开“高级设置”部分。****

    ![创建访问评审 - 高级设置](./media/create-access-review/advanced-settings.png)

1. 将“显示建议”设置为“启用”，以基于用户的访问权限信息向评审者显示系统建议。********

1. 将“需要提供审批原因”设置为“启用”，以要求审阅者提供批准原因。********

1. 将“邮件通知”设置为“启用”，以便在访问评审开始时让 Azure AD 向评审者发送电子邮件通知，并在评审完成时向管理员发送电子邮件通知。********

1. 将“提醒”设置为“启用”，让 Azure AD 向尚未完成其审阅的审阅者发送访问评审正在进行的提醒。********

    默认情况下，Azure AD 自动在中途向还未作出回复的审阅者发送结束日期提醒。

## <a name="start-the-access-review"></a>启动访问评审

指定访问评审的设置后，单击“启动”。**** 访问评审将显示在列表中，并带有其状态指示器。

![访问评审及其状态的列表](./media/create-access-review/access-reviews-list.png)

默认情况下，在评审开始后不久，Azure AD 会向评审者发送一封电子邮件。 如果选择不让 Azure AD 发送电子邮件，请务必通知评审者有一个访问评审任务等待他们完成。 可以向他们显示有关如何[评审对组或应用程序的访问权限](perform-access-review.md)的说明。 如果评审工作是让来宾评审他们自己的访问权限，则可以显示有关如何[评审自己对组或应用程序的访问权限](review-your-access.md)的说明。

如果将来宾指定为评审者，但他们尚未接受邀请，则他们不会收到来自访问评审的电子邮件，因为在评审前必须先接受邀请。

## <a name="access-review-status-table"></a>访问审核状态表

| 状态 | 定义 |
|--------|------------|
|NotStarted | 已创建审核，用户发现正在等待启动。 |
|正在初始化   | 用户发现正在进行中，以标识属于审核的所有用户。 |
|正在启动 | 审核正在启动。 如果启用了电子邮件通知，则向审阅者发送电子邮件。 |
|正在进行 | 审核已经开始。 如果启用了电子邮件通知，则电子邮件已发送给审阅者。 审阅者可以提交决策，直到截止日期。 |
|完成 | 审核已完成，电子邮件正在发送给审核所有者。 |
|自动查看 | 审核处于系统审核阶段。 系统正在记录未根据建议或预配置决策进行审核的用户的决定。 |
|自动审核 | 系统已为未审核的所有用户记录决策。 如果启用了"自动应用"，则审核已准备就绪，可以继续**应用**。 |
|应用 | 对于已审核的用户，访问权限不会发生变化。 |
|已应用 | 已拒绝的用户（如果有）已从资源或目录中删除。 |

## <a name="create-reviews-via-apis"></a>通过 API 创建评审

也可以使用 API 创建访问评审。 在 Azure 门户中管理组和应用程序用户的访问评审的方法也可以使用 Microsoft Graph API 来实现。 有关详细信息，请参阅 Azure [AD 访问审阅 API 引用](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta)。 有关代码示例，请参阅[通过 Microsoft 图形检索 Azure AD 访问审核的示例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)。

## <a name="next-steps"></a>后续步骤

- [评审组或应用程序的访问权限](perform-access-review.md)
- [评审自己对组或应用程序的访问权限](review-your-access.md)
- [完成组或应用程序的访问评审](complete-access-review.md)

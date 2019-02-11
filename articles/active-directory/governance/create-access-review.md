---
title: 使用 Azure AD 针对有权访问应用程序的组成员或用户创建访问评审 | Microsoft Docs
description: 了解如何针对有权访问应用程序的组成员或用户创建访问评审。
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/15/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 1c71333a49643647bae3730e4f14f26939388c4f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153737"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>使用 Azure AD 针对组成员或应用程序访问权限创建访问评审

员工和来宾对组和应用程序的访问权限会不断变化。 为了降低与过期访问权限分配相关的风险，管理员可以使用 Azure Active Directory (Azure AD) 针对组成员或应用程序访问权限创建访问评审。 如果需要定期评审访问权限，则还可以创建定期访问评审。 有关这些方案的详细信息，请参阅[管理用户访问权限](manage-user-access-with-access-reviews.md)和[管理来宾访问权限](manage-guest-access-with-access-reviews.md)。

本文介绍如何针对组成员或应用程序访问权限创建新的访问评审。

## <a name="prerequisites"></a>先决条件

- [访问评审已启用](access-reviews-overview.md)
- 全局管理员或帐户管理员

## <a name="create-an-access-review"></a>创建访问评审

1. 登录到 Azure 门户并打开[“访问评审”页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 单击“控制”。

1. 单击“新建访问评审”创建新的访问评审。

    ![访问评审 - 控制](./media/create-access-review/controls.png)

1. 命名访问评审。 可选择为评审提供说明。 名称和说明向评审者显示。

    ![创建访问评审 - 评审名称和说明](./media/create-access-review/name-description.png)

1. 设置“开始日期”。 默认情况下，访问评审只进行一次，从创建的时候开始，在一个月内结束。 可以更改开始和结束日期，使访问评审在将来的时间开始，并持续所需的天数。

    ![创建访问评审 - 开始和结束日期](./media/create-access-review/start-end-dates.png)

1. 若要让访问评审定期进行，请将“频率”设置从“一次”更改为“每周”、“每月”、“每季”或“每年”，并使用“持续时间”滑块或文本框来定义定期进行的一系列评审每次的运行天数（可供审阅者输入）。 例如，每月评审的最长持续时间可以设置为 27 天，以免评审时间重叠。

1. 使用“结束”设置指定如何结束定期访问评审系列。 系列的结束方式有三种：持续运行，无限期地开始评审；运行至指定日期；运行至已完成定义的评审数目。 你、其他用户帐户管理员或其他全局管理员可以在创建后停止此系列，只需在“设置”中更改日期，然后此系列就会在该日期结束。

1. 在“用户”部分，指定访问评审要应用到的用户。 访问评审的对象可以是组成员，或者是已分配到应用程序的用户。 可将访问评审的范围进一步限定为仅评审属于成员（或已分配到应用程序）的来宾用户，而不是评审属于成员或有权访问应用程序的所有用户。

    ![创建访问评审 - 用户](./media/create-access-review/users.png)

1. 在“评审者”部分选择一人或多人来评审范围内的所有用户。 也可以选择让成员评审自己的访问权限。 如果资源是一个组，可以要求组的所有者进行评审。 还可以要求评审者在审批访问权限时提供原因。

    ![创建访问评审 - 评审者](./media/create-access-review/reviewers.png)

1. 在“计划”部分选择要使用的计划。 可以将访问评审组织为计划，以便针对不同目的简化跟踪和收集访问评审的方式。 “默认计划”将始终存在，但也可以创建不同的计划。 例如，可以选择针对每个符合性措施或业务目标创建一个计划。

    ![创建访问评审 - 计划](./media/create-access-review/programs.png)

### <a name="upon-completion-settings"></a>完成设置后

1. 若要指定评审完成后发生的情况，请展开“完成后的设置”部分。

    ![完成后的设置](./media/create-access-review/upon-completion-settings.png)

1. 若要自动删除被拒绝用户的访问权限，请将“将结果自动应用到资源”设置为“启用”。 若要在评审完成后手动应用结果，请将开关设置为“禁用”。

1. 使用“如果审阅者未答复”列表指定对于审阅者在评审期限内未评审的用户要执行的操作。 此设置不影响审阅者已手动评审的用户。 如果最终的审阅者决策是“拒绝”，则会删除用户的访问权限。

    - **不更改** - 将用户访问权限保持不变
    - **删除访问权限** - 删除用户的访问权限
    - **批准访问权限** - 批准用户的访问权限
    - **采用建议** - 根据系统的建议拒绝或批准用户的持续访问权限

### <a name="advanced-settings"></a>高级设置

1. 若要指定其他设置，请展开“高级设置”部分。

    ![高级设置](./media/create-access-review/advanced-settings.png)

1. 将“显示建议”设置为“启用”，以基于用户的访问权限信息向评审者显示系统建议。

1. 将“需要提供审批原因”设置为“启用”，以要求审阅者提供批准原因。

1. 将“邮件通知”设置为“启用”，以便在访问评审开始时让 Azure AD 向评审者发送电子邮件通知，并在评审完成时向管理员发送电子邮件通知。

1. 将“提醒”设置为“启用”，让 Azure AD 向尚未完成其审阅的审阅者发送访问评审正在进行的提醒。

## <a name="start-the-access-review"></a>启动访问评审

指定访问评审的设置后，单击“启动”。

默认情况下，在评审开始后不久，Azure AD 会向评审者发送一封电子邮件。 如果选择不让 Azure AD 发送电子邮件，请务必通知评审者有一个访问评审任务等待他们完成。 可以向评审者显示如何[评审访问](perform-access-review.md)的说明。 如果评审工作是让来宾评审他们自己的访问权限，则可以显示有关如何[评审自己的访问权限](perform-access-review.md)的说明。

如果某些评审者是来宾，则仅当他们已接受邀请时，才会通过电子邮件向他们发出通知。

## <a name="manage-the-access-review"></a>管理访问审阅

当评审者在 Azure AD 仪表板中完成其评审时，可以在“访问评审”部分中跟踪进度。 在[评审完成](complete-access-review.md)之前，目录中的任何访问权限都不会更改。

如果这是一次性评审，则请在访问评审期限结束后或管理员停止了访问评审后，按照[完成访问评审](complete-access-review.md)中的步骤查看并应用结果。  

若要管理一系列访问评审，请从“控件”导航到访问评审，此时会在“计划的评审”中找到即将进行的评审，然后即可相应地编辑结束日期或添加/删除评审者。 

根据你在“完成后操作”设置中的选择，自动应用会在评审者的结束日期之后执行，或在你手动停止评审后执行。 评审状态将从“已完成”变为各种中间状态（例如“正在应用”），并最终变为“已应用”状态。 几分钟后，应会看到被拒绝的用户（如果有）已从组成员身份或应用程序分配中删除。

## <a name="create-reviews-via-apis"></a>通过 API 创建评审

也可以使用 API 创建访问评审。 在 Azure 门户中管理组和应用程序用户的访问评审的方法也可以使用 Microsoft Graph API 来实现。 有关详细信息，请参阅 [Azure AD 访问评审 API 参考](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/accessreviews_root)。 有关代码示例，请参阅[通过 Microsoft Graph 检索 Azure AD 访问评审的示例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 访问评审来启动访问评审](perform-access-review.md)
- [在 Azure AD 中完成组成员或用户对应用程序的访问权限的访问评审](complete-access-review.md)

---
title: 使用 Azure AD 针对有权访问应用程序的组成员或用户创建访问评审 | Microsoft Docs
description: 了解如何针对有权访问应用程序的组成员或用户创建访问评审。
services: active-directory
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: compliance-reports
ms.date: 05/14/2018
ms.author: rolyon
ms.openlocfilehash: 6a1d4a55074630cc20c98202d2e94ec682c323f4
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234067"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>使用 Azure AD 针对组成员或应用程序访问权限创建访问评审

当用户不再需要访问权限时，访问权限分配会“过时”。 为了降低与过期访问权限分配相关的风险，管理员可以使用 Azure Active Directory (Azure AD) 对已分配到应用程序的组成员或用户创建访问评审。 创建定期访问评审可以节省时间。 如果需要定期评审有权访问应用程序或属于某个组的用户，可以定义这些评审的频率。 有关这些方案的详细信息，请参阅[管理用户访问权限](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)和[管理来宾访问权限](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)。 

## <a name="create-an-access-review"></a>创建访问评审

1. 以全局管理员的身份转到[访问评审页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)，并选择“计划”。

2. 选择包含要创建的访问评审控件的计划。 “默认计划”将始终存在，但也可以创建不同的计划。 例如，可以选择针对每个符合性措施或业务目标创建一个计划。

3. 在计划中，选择“控件”，然后选择“添加”来添加控件。

4. 命名访问评审。 可选择为评审提供说明。 名称和说明向评审者显示。

5. 设置开始日期。 默认情况下，访问评审只进行一次，从创建的时候开始，在一个月内结束。 可以更改开始和结束日期，使访问评审在将来的时间开始，并持续所需的天数。

6. 若要让访问评审定期进行，可将频率从“一次”更改为“每周”、“每月”、“每季”或“每年”，并且可以通过滑块或文本框来定义定期进行的一系列评审每次的运行天数（可供审阅者输入）。 例如，每月评审的最长持续时间可以设置为 27 天，以免评审时间重叠。 

7.  定期访问评审系列的结束方式有 3 种：持续运行，无限期地开始评审；运行至指定日期；运行至已完成定义的评审数目。 你或者其他全局管理员可以在创建此系列后将其停止，只需在“设置”中更改日期，然后此系列就会在该日期结束。

8. 访问评审的对象可以是组成员，或者是已分配到应用程序的用户。 可将访问评审的范围进一步限定为仅评审属于成员（或已分配到应用）的来宾用户，而不是评审属于成员或有权访问应用程序的所有用户。

9. 选择一人或多人来评审范围内的所有用户。 也可以选择让成员评审自己的访问权限。 如果资源是一个组，可以要求组的所有者进行评审。 还可以要求评审者在审批访问权限时提供原因。

10. 如果希望在评审完成后手动应用结果，请单击“启动”。  也可查看下一部分，了解如何将评审配置为自动应用结果。

### <a name="configuring-an-access-review-with-auto-apply"></a>使用自动应用来配置访问评审

1.  展开“完成后操作”设置对应的菜单，然后启用“自动向资源应用结果”。 

2.  如果审阅者未在评审期完成用户的评审，则可让访问评审在拒绝/批准用户的继续访问权限时采用系统的建议（如果此功能已启用），也可保留其访问权限不变或删除其访问权限。 这不会影响评审者手动评审过的用户。如果评审者的最终决定是“拒绝”，则会删除用户的访问权限。

3.  若要在审阅者不响应的情况下启用采用建议这一选项，请展开“高级”设置并启用“显示建议”。
 
4.  最后，单击“开始”。

根据你在“完成后操作”设置中的选择，自动应用会在评审者的结束日期之后执行，或在你手动停止评审后执行。 评审状态将从“已完成”变为各种中间状态（例如“正在应用”），并最终变为“已应用”状态。 几分钟后，应当会看到被拒绝的用户（如果有）被从组成员身份或应用分配中删除。


## <a name="manage-the-access-review"></a>管理访问审阅

默认情况下，在评审开始后不久，Azure AD 会向评审者发送一封电子邮件。 如果选择不让 Azure AD 发送电子邮件，请务必通知评审者有一个访问评审任务等待他们完成。 可以向评审者显示如何[评审访问](active-directory-azure-ad-controls-perform-access-review.md)的说明。 如果评审工作是让来宾评审他们自己的访问权限，则可以显示有关如何[评审自己的访问权限](active-directory-azure-ad-controls-perform-access-review.md)的说明。

如果某些评审者是来宾，则仅当他们已接受邀请时，才会通过电子邮件向他们发出通知。

若要管理一系列访问评审，请从“控件”导航到访问评审，此时会在“计划的评审”中找到即将进行的评审，然后即可相应地编辑结束日期或添加/删除评审者。 

当评审者在 Azure AD 仪表板中完成其评审时，可以在“访问评审”部分中跟踪进度。 在[评审完成](active-directory-azure-ad-controls-complete-access-review.md)之前，目录中的任何访问权限都不会更改。

## <a name="next-steps"></a>后续步骤

启动访问评审后，Azure AD 会自动向评审者发送一封电子邮件，提示他们评审访问权限。 如果用户未收到电子邮件，可以向他们发送如何[评审访问权限](active-directory-azure-ad-controls-perform-access-review.md)的说明。 

如果这是一次性评审，则请在访问评审期限结束后或管理员停止了访问评审后，按照[完成访问评审](active-directory-azure-ad-controls-complete-access-review.md)中的步骤查看并应用结果。  

如果这是一个评审系列，则请导航到访问评审系列页中的“评审历史记录”，以便选择已完成的访问评审。  未来的评审将列在“计划的评审”下，你可以在其中编辑持续时间，以及添加或删除单个评审的评审者。

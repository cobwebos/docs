---
title: 完成对组或应用程序的访问评审-Azure Active Directory |Microsoft Docs
description: 了解如何在 Azure Active Directory 访问评审中完成组成员或应用程序访问的访问评审。
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c405032368ffd06f5808bc4518302d2f6d66b9
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489145"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>在 Azure AD 访问评审中完成对组或应用程序的访问评审

作为管理员, 你可以[创建组、应用程序和审阅者的访问评审](create-access-review.md), 以[执行访问评审](perform-access-review.md)。 本文介绍如何查看访问评审的结果并应用结果。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>先决条件

- Azure AD Premium P2
- 全局管理员、用户管理员、安全管理员或安全读者

有关详细信息, 请参阅[哪些用户必须拥有许可证？](access-reviews-overview.md#which-users-must-have-licenses)。

## <a name="view-an-access-review"></a>查看访问评审

您可以在评审者完成评审后跟踪进度。

1. 登录到 Azure 门户并打开 "[标识管理" 页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 在左侧菜单中, 单击 "**访问评审**"。

1. 在列表中, 单击 "访问评审"。

    若要查看一系列访问评审, 请导航到 "访问评审", 并在计划的评审中找到即将发生的情况。

    在 "**概述**" 页上, 可以查看进度。 在评审完成之前, 不会更改目录中的访问权限。

    ![访问评审进度](./media/complete-access-review/overview-progress.png)

1. 如果要在访问评审结束之前停止访问评审, 请单击 "**停止**" 按钮。

    当停止审阅时, 审阅者将无法再给出响应。 停止后将无法重新开始评审。

1. 如果你不再想要访问评审, 可以通过单击 "**删除**" 按钮将其删除。

## <a name="apply-the-changes"></a>应用更改

如果已启用 "**自动将结果应用于资源**" 并基于您在**完成设置**中所做的选择, 则将在评审结束日期之后或在您手动停止审阅时执行自动应用。

如果未为审核启用 "**自动应用结果**", 请单击 "**应用**" 以手动应用更改。 如果在评审中拒绝了某个用户的访问权限, 则当你单击 "**应用**" 时, Azure AD 会删除其成员身份或应用程序分配。

![应用访问评审更改](./media/complete-access-review/apply-changes.png)

该评审的状态将从 "**已完成**" 更改为 "已完成", 如 "应用 **" 和 "** 最终 **"。** 几分钟后，应会看到被拒绝的用户（如果有）已从组成员身份或应用程序分配中删除。

为评审配置自动应用或者选择“应用”不会影响源自本地目录的组，也不会影响动态组。 若要更改源自本地的组，请下载结果，并将这些更改应用到该目录中组的表示形式。

## <a name="retrieve-the-results"></a>检索结果

若要查看一次性访问评审的结果, 请单击 "**结果**" 页。 若要只查看用户的访问权限, 请在 "搜索" 框中, 键入查看其访问权限的用户的显示名称或用户主体名称。

![检索访问评审的结果](./media/complete-access-review/retrieve-results.png)

若要查看定期活动访问评审的进度, 请单击 "**结果**" 页。

若要查看定期访问评审的已完成实例的结果, 请单击 "**查看历史记录**", 然后根据实例的开始日期和结束日期从已完成的访问评审实例列表中选择特定实例。 可以从 "**结果**" 页获取此实例的结果。

若要检索访问评审的所有结果, 请单击 "**下载**" 按钮。 可以在 Excel 中或在可打开 UTF-8 编码 CSV 文件的其他程序中查看生成的 CSV 文件。

## <a name="remove-users-from-an-access-review"></a>从访问评审中删除用户

 默认情况下，删除的用户将在 Azure AD 中保持删除状态 30 天，在此期间，管理员可以根据需要还原这些用户。  30 天后，该用户将被永久删除。  此外，使用 Azure Active Directory 门户，全局管理员可以在达到该时间段之前，显式地[永久删除最近删除的用户](../fundamentals/active-directory-users-restore.md)。  某个用户被永久删除后，随后有关该用户的数据将从活动访问评审中删除。  有关已删除用户的审核信息仍保留在审核日志中。

## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 访问评审管理用户访问权限](manage-user-access-with-access-reviews.md)
- [使用 Azure AD 访问评审管理来宾访问权限](manage-guest-access-with-access-reviews.md)
- [创建组或应用程序的访问评审](create-access-review.md)
- [针对充当 Azure AD 管理角色的用户创建访问评审](../privileged-identity-management/pim-how-to-start-security-review.md)

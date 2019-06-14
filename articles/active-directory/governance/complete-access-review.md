---
title: 完成的组或应用程序-Azure Active Directory 访问评审 |Microsoft Docs
description: 了解如何完成访问评审的组成员或在 Azure Active Directory 访问评审中的应用程序访问。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/22/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec3909ffbb624284f999360140b7454098643062
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66473373"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>完成访问评审的组或 Azure AD 中的应用程序访问评审

作为管理员，你[创建组或应用程序的访问评审](create-access-review.md)和审阅者[执行访问评审](perform-access-review.md)。 本文介绍如何查看访问评审的结果并应用结果。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>必备组件

- Azure AD Premium P2
- 全局管理员、 用户管理员、 安全管理员或安全读者

有关详细信息，请参阅[哪些用户必须具有许可证？](access-reviews-overview.md#which-users-must-have-licenses)。

## <a name="view-an-access-review"></a>查看访问评审

审阅者完成其审阅时，可以跟踪进度。

1. 登录到 Azure 门户，然后打开[标识监管页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 在左侧菜单中，单击**访问评审**。

1. 在列表中，单击访问评审。

    若要查看一系列的访问评审会议中，导航到访问评审，并将计划评审中找到即将推出的匹配项。

    上**概述**页上，你可以查看进度。 没有访问权限的目录中已更改，直到完成评审。

    ![访问评审进度](./media/complete-access-review/overview-progress.png)

1. 如果你想要停止访问评审之前已到达计划的结束日期，请单击**停止**按钮。

    当停止评审，审阅者将不再能够提供响应。 停止后将无法重新开始评审。

1. 如果您不再希望在访问评审中，您可以通过单击删除它**删除**按钮。

## <a name="apply-the-changes"></a>应用更改

如果**自动向资源应用结果**已启用，并基于所选内容中**完成设置后**，自动-应用将审阅的结束日期或手动停止评审后执行。

如果**自动向资源应用结果**未启用评审后，单击**应用**手动应用所做的更改。 如果用户的访问评审中拒绝了，当您单击时**应用**，Azure AD 会删除其成员身份或应用程序分配。

![应用访问评审更改](./media/complete-access-review/apply-changes.png)

此评审的状态将更改从**已完成**中间状态，例如通过**应用**，最后到状态**结果应用**。 几分钟后，应会看到被拒绝的用户（如果有）已从组成员身份或应用程序分配中删除。

为评审配置自动应用或者选择“应用”不会影响源自本地目录的组，也不会影响动态组  。 若要更改源自本地的组，请下载结果，并将这些更改应用到该目录中组的表示形式。

## <a name="retrieve-the-results"></a>检索结果

若要查看的一次性访问评审结果，请单击**结果**页。 若要查看只是用户的访问权限，在搜索框中，键入显示名称或已评审其访问权限的用户的用户主体名称。

![检索访问评审的结果](./media/complete-access-review/retrieve-results.png)

若要查看的重复进行的活动的访问评审进度，请单击**结果**页。

若要查看的重复进行的访问评审已完成实例的结果，请单击**查看历史记录**，然后选择已完成的访问评审实例，从列表中的特定实例基于实例的开始和结束日期。 可以从获取此实例的结果**结果**页。

若要检索的访问评审的所有结果，请单击**下载**按钮。 可以在 Excel 中或在可打开 UTF-8 编码 CSV 文件的其他程序中查看生成的 CSV 文件。

## <a name="remove-users-from-an-access-review"></a>删除访问评审的用户

 默认情况下，删除的用户将在 Azure AD 中保持删除状态 30 天，在此期间，管理员可以根据需要还原这些用户。  30 天后，该用户将被永久删除。  此外，使用 Azure Active Directory 门户，全局管理员可以在达到该时间段之前，显式地[永久删除最近删除的用户](../fundamentals/active-directory-users-restore.md)。  某个用户被永久删除后，随后有关该用户的数据将从活动访问评审中删除。  有关已删除用户的审核信息仍保留在审核日志中。

## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 访问评审管理用户访问权限](manage-user-access-with-access-reviews.md)
- [使用 Azure AD 访问评审管理来宾访问权限](manage-guest-access-with-access-reviews.md)
- [创建组或应用程序的访问评审](create-access-review.md)
- [针对充当 Azure AD 管理角色的用户创建访问评审](../privileged-identity-management/pim-how-to-start-security-review.md)

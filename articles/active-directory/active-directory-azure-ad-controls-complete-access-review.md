---
title: 使用 Azure AD 完成组成员或用户对应用程序进行访问的访问评审 | Microsoft Docs
description: 了解如何在 Azure Active Directory 中针对有权访问应用程序的组成员或用户完成访问评审。
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
ms.component: compliance-reports
ms.date: 05/02/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 3b8fb474f163922844cf9b28568be58cf8cf62e1
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38307343"
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>在 Azure AD 中完成组成员或用户对应用程序进行访问的访问评审

管理员可以使用 Azure Active Directory (Azure AD) 对已分配到应用程序的组成员或用户[创建访问评审](active-directory-azure-ad-controls-create-access-review.md)。 Azure AD 会自动向评审者发送一封电子邮件，提示他们评审访问权限。 如果用户未收到电子邮件，可以向他们发送[评审访问权限](active-directory-azure-ad-controls-perform-access-review.md)中的说明。 （注意，被指定为评审者但尚未接受邀请的来宾不会收到来自访问评审的电子邮件，因为他们在评审前必须先接受邀请。）访问评审期限结束后，或者管理员停止了访问评审后，请遵循本文中的步骤查看并应用结果。

## <a name="view-an-access-review-in-the-azure-portal"></a>在 Azure 门户中查看访问评审

1. 转到[访问评审页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)，选择“计划”，然后选择包含访问评审控制措施的计划。

2. 选择“管理”并选择访问评审控制措施。 如果计划中有许多控制措施，可以按特定类型筛选控制措施，并按状态进行排序。 还可以按访问评审控制措施的名称或创建此措施的所有者的显示名称进行搜索。 

## <a name="stop-a-review-that-hasnt-finished"></a>停止尚未完成的评审

如果评审尚未到达计划结束日期，管理员可以选择“停止”提前结束评审。 停止评审后，将无法继续评审用户。 停止后将无法重新开始评审。

## <a name="apply-the-changes"></a>应用更改 

如果访问评审因为已到达结束日期或者因为管理员已手动将其停止而已完成，并且没有为评审配置自动应用，则可以选择“应用”来手动应用更改。 更新组或应用程序即可执行评审结果。 如果在评审中拒绝了某个用户的访问权限，当管理员选择此选项时，Azure AD 会删除该用户的成员资格或应用程序分配。 

如果访问评审已完成并且配置了自动应用，则评审状态将从“已完成”更改为各种中间状态，并且最终将更改为“已应用”状态。 在几分钟后，应当会看到被拒绝的用户（如果有）被从资源组成员身份或应用分配中删除。

为评审配置自动应用或者选择“应用”不会影响源自本地目录的组，也不会影响动态组。 若要更改源自本地的组，请下载结果，并将这些更改应用到该目录中组的表示形式。

## <a name="download-the-results-of-the-review"></a>下载评审结果

若要检索评审结果，请选择“审批”，然后选择“下载”。 可以在 Excel 中或在可打开 UTF-8 编码 CSV 文件的其他程序中查看生成的 CSV 文件。

## <a name="optional-delete-a-review"></a>可选：删除评审
如果不再需要关注评审，则可以将其删除。 选择“删除”即可从 Azure AD 中删除评审。

> [!IMPORTANT]
> 删除之前不会出现任何警告，因此请确定想要删除该评审。
> 
> 

## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 访问评审管理用户访问权限](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [使用 Azure AD 访问评审管理来宾访问权限](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [管理 Azure AD 访问评审的计划和控制措施](active-directory-azure-ad-controls-manage-programs-controls.md)
- [创建对组成员的访问评审或对应用程序的访问](active-directory-azure-ad-controls-create-access-review.md)
- [针对充当 Azure AD 管理角色的用户创建访问评审](privileged-identity-management/pim-how-to-start-security-review.md)

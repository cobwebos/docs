---
title: "使用 Azure AD 完成组成员或用户对应用程序进行访问的访问评审 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 中针对有权访问应用程序的组成员或用户完成访问评审。"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: e83bc42d658c4d6304f98b99d0e0942595bd65d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>在 Azure AD 中完成组成员或用户对应用程序进行访问的访问评审

管理员可以通过[创建访问评审](active-directory-azure-ad-controls-create-access-review.md)，要求评审已分配到应用程序的组成员或用户。 Azure AD 会自动向评审者发送一封电子邮件，提示他们评审访问权限。 如果用户未收到电子邮件，可以向他们发送[如何评审访问权限](active-directory-azure-ad-controls-perform-access-review.md)中的说明。  访问评审期限结束后，或者管理员停止了访问评审后，请遵循本文中的步骤查看并应用结果。

## <a name="viewing-an-access-review-in-the-azure-portal"></a>在 Azure 门户中查看访问评审

1. 转到[访问评审页面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)，切换到“计划”选项卡，选择包含访问评审控制措施的计划。
2. 单击“管理”并选择访问评审控制措施。  如果计划中包含许多控制措施，可以通过筛选来仅显示特定类型的控制措施、按状态排序，或者按访问评审控制措施的名称或控制措施创建者的显示名称进行搜索。 

## <a name="stopping-a-review-that-has-not-yet-completed"></a>停止尚未完成的评审

如果评审尚未达到计划的结束日期，管理员可以通过单击“停止”按钮来停止该评审，以提前完成评审。  如果此时还有未审阅的用户，他们在停止审阅后将无法再得到审阅。 停止后，无法重新开始审阅。

## <a name="applying-the-changes"></a>应用更改 

由于已达到结束日期或者管理员手动停止而完成访问评审后，单击“应用”按钮可以通过更新组或应用程序来实施评审结果。 如果在评审中拒绝了某个用户的访问权限，则管理员单击此按钮时，Azure AD 会删除该用户的成员身份或应用程序分配。 

单击“应用”按钮不会影响源自本地目录的组，也不影响动态组。  若要更改源自本地的组，请下载结果，并将这些更改应用到该目录中组的表示形式。

## <a name="downloading-the-results-of-the-review"></a>下载评审结果

若要检索评审结果，将切换到“审批”选项卡，并单击“下载”按钮。  可以在 Excel 或其他可打开 CSV 文件的程序中查看生成的 CSV 文件。

## <a name="optional-deleting-a-review"></a>可选：删除评审
如果不想要进一步了解审阅，请将其删除。 单击“删除”按钮会从 Azure AD 中删除评审。

> [!IMPORTANT]
> 在删除之前不会出现任何警告，因此请确定想要删除该评审。
> 
> 

## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 访问评审管理用户访问权限](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [使用 Azure AD 访问评审管理来宾访问权限](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [管理 Azure AD 访问评审的计划和控制措施](active-directory-azure-ad-controls-manage-programs-controls.md)
- [创建对组成员的访问评审或对应用程序的访问](active-directory-azure-ad-controls-create-access-review.md)
- [针对充当 Azure AD 管理角色的用户创建访问评审](active-directory-privileged-identity-management-how-to-start-security-review.md)

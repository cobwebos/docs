---
title: 检索 Azure AD 访问评审结果 | Microsoft Docs
description: 如何检索 Azure Active Directory 访问评审结果。
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 05/16/2018
ms.author: rolyon
ms.openlocfilehash: c30d166335e31cdbf03283371da42fa6b7cda162
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233108"
---
# <a name="retrieve-access-review-results"></a>检索访问评审结果

管理员可以使用 Azure Active Directory (Azure AD) 对已分配到应用程序的组成员或用户[创建访问评审](active-directory-azure-ad-controls-create-access-review.md)。  具有**全局管理员**、**安全管理员**或**安全读者**角色的用户也可以读取访问评审结果。  为了将用户分配给其中一个角色，特权角色管理员可以使用 Azure AD PIM 来让用户有资格激活该角色，或者全局管理员可以永久地[将用户分配给角色](active-directory-users-assign-role-azure-portal.md)。

[!INCLUDE [Privacy](../../includes/gdpr-intro-sentence.md)]

## <a name="locating-an-access-review"></a>查找访问评审

如果知道哪个计划包含访问评审，请转到“访问评审”页，选择“计划”，然后选择包含访问评审控制措施的计划。  选择“控制措施”并选择访问评审控制措施。 如果计划中有许多控制措施，可以按特定类型筛选控制措施，并按状态进行排序。 还可以按访问评审控制措施的名称或创建此措施的所有者的显示名称进行搜索。 

如果不知道哪个计划包含访问评审，请转到“访问评审”页，然后选择“控制措施”。  可以筛选特定类型的控制措施、按其状态排序，或者按访问评审控制措施的名称或创建控制措施的所有者显示名称进行搜索。 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>检索一次性访问评审的结果

如果评审重复类型为一次，则可以从“结果”部分获取活动访问评审的进度和已完成访问评审的结果。  可以输入正在接受访问评审的用户的显示名称或用户主体名称，以直接查看该用户的访问。  若要检索已完成访问评审的所有结果，请单击“下载”按钮。

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>检索定期访问评审的多个实例的结果

若要查看定期进行的活动访问评审的进度，请单击“结果”部分。  可以输入正在接受访问评审的用户的显示名称或用户主体名称。

若要查看已完成的定期访问评审实例的结果，请选择“查看历史记录”，然后根据实例的开始日期和结束日期，从已完成的访问评审实例列表中选择特定实例。   该实例的结果可以从“结果”部分获得。  可以输入正在接受访问评审的用户的显示名称或用户主体名称，以直接查看该用户的访问。  若要检索已完成的定期访问评审实例的所有结果，请单击“下载”按钮。


## <a name="removing-users-from-an-access-review"></a>从访问评审中删除用户

默认情况下，删除的用户将在 Azure AD 中保持删除状态 30 天，在此期间，管理员可以根据需要还原这些用户。  30 天后，该用户将被永久删除。  此外，使用 Azure Active Directory 门户，全局管理员可以在达到该时间段之前，显式地[永久删除最近删除的用户](active-directory-users-restore.md)。  某个用户被永久删除后，随后有关该用户的数据将从活动访问评审中删除。  有关已删除用户的审核信息仍保留在审核日志中。

## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 访问评审管理用户访问权限](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [使用 Azure AD 访问评审管理来宾访问权限](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [管理 Azure AD 访问评审的计划和控制措施](active-directory-azure-ad-controls-manage-programs-controls.md)
- [创建对组成员的访问评审或对应用程序的访问](active-directory-azure-ad-controls-create-access-review.md)
- [针对充当 Azure AD 管理角色的用户创建访问评审](active-directory-privileged-identity-management-how-to-start-security-review.md)



---
title: 以来宾用户身份离开组织 - Azure Active Directory | Microsoft Docs
description: 演示 Azure AD B2B 来宾用户如何使用访问面板离开组织。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 942439da3e116415c77a28950df69d44744b2dd8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077702"
---
# <a name="leave-an-organization-as-a-guest-user"></a>以来宾用户身份离开组织

如果 Azure Active Directory (Azure AD) B2B 来宾用户不再需要使用来自某个组织的应用或保持任何关联，则可随时决定离开该组织。 用户可自行离开组织，无需联系管理员。

## <a name="leave-an-organization"></a>离开组织

若要以“访问面板”登录用户的身份离开组织，请执行以下操作[](https://myapps.microsoft.com)：

1. 如果尚未登录要离开的组织，请在右上角选择你的姓名，然后单击要离开的组织。
2. 在右上角选择你的姓名。
3. 在“组织”旁边，选择设置图标（齿轮）。
 
   ![显示访问面板中用户设置的屏幕截图](media/active-directory-b2b-leave-the-organization/UserSettings.png) 

3. 在“组织”下，找到要离开的组织，然后选择“离开组织”。

   ![显示用户界面中“离开组织”选项的屏幕截图](media/active-directory-b2b-leave-the-organization/LeaveOrg.png)

4. 系统要求确认时，请选择“离开”。 

## <a name="account-removal"></a>删除帐户

当用户离开组织时，用户帐户会在目录中被“软删除”。 默认情况下，用户对象会移至 Azure AD 中的“删除的用户”区域，但在 30 天后才会永久删除。 如果用户在 30 天内请求恢复帐户，管理员可借助此软删除功能恢复用户帐户（包括群组和权限）。

如果需要，租户管理员可以在 30 天期间内的任何时间永久删除帐户。 为此，请按以下步骤操作：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”。
2. 在“管理”下，选择“用户”。
3. 选择“删除的用户”。
4. 选中删除的用户旁边的复选框，然后选择“永久删除”。

如果永久删除用户，此操作不可撤销。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>后续步骤

- 有关 Azure AD B2B 的概述，请参阅[什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)




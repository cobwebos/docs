---
title: "如何激活或停用角色 | Microsoft 文档"
description: "了解如何使用 Azure Privileged Identity Management 应用程序激活特权标识的角色。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 1ce9e2e7-452b-4f66-9588-0d9cd2539e45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 67695e5715e83e2214b27a2abd76a3f68fb4a75a


---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>如何在 Azure AD Privileged Identity Management 中激活或停用角色
Azure Active Directory (AD) Privileged Identity Management 简化了企业管理以特权身份访问 Azure AD 中的资源和其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）的方式。  

如果你符合管理角色的资格，即表示可以在需要执行必须具有该角色才能执行的任务时激活该角色。 例如，如果偶尔管理 Office 365 功能，则组织的特权角色管理员可能不会让你成为永久全局管理员，因为该角色也影响其他服务。 他们会让你符合 Azure AD 角色（例如“Exchange Online 管理员”）的资格。 可以在需要权限时，请求暂时分配该角色，然后将在预定的时段内拥有管理控制权。

本文面向需要在 Azure AD Privileged Identity Management (PIM) 中激活其角色的管理员。 其中逐步讲解了如何在需要权限时激活角色，在完成任务后停用该角色。

## <a name="add-the-privileged-identity-management-application"></a>添加 Privileged Identity Management 应用程序
在 [Azure 门户中](https://portal.azure.com/)使用 Azure AD Privileged Identity Management 应用程序请求角色激活，即使要在另一个门户中或通过 PowerShell 来操作，也是如此。 如果 Azure 门户中没有 Azure AD Privileged Identity Management 应用程序，请遵循以下步骤开始操作。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择 Azure 门户右上角的用户名，然后选择要操作的目录。
3. 选择“更多服务”，然后使用“筛选器”文本框搜索“Azure AD Privileged Identity Management”。
4. 选中“固定到仪表板”，然后单击“创建”。 Privileged Identity Management 应用程序打开。

## <a name="activate-a-role"></a>激活角色
需要充当某个角色时，可以使用 Azure AD Privileged Identity Management 应用程序中的“激活我的角色”按钮来请求激活。

1. 登录到 [Azure 门户](https://portal.azure.com/)，选择“Azure AD Privileged Identity Management”磁贴。
2. 选择“激活我角色”。 此时将显示已分配给你的角色。
3. 选择要激活的角色。
4. 选择“激活”。 此时将显示“请求激活角色”边栏选项卡。
5. 有些角色要求事先进行多重身份验证 (MFA) 才能激活。 只需在每个会话中执行身份验证一次。
   
    ![在激活角色之前先执行 MFA 身份验证 - 屏幕截图][2]
6. 在文本字段中输入请求激活的原因。  某些角色要求提供问题票证编号。
7. 选择“确定”。  角色现已激活，同时，角色更改将显示在 Microsoft Online Services 中。

## <a name="deactivate-a-role"></a>停用角色
角色激活后，在达到时间限制时会自动停用。

如果提前完成了任务，也可以在 Azure AD Privileged Identity Management 应用程序中手动停用角色。  选择“激活我的角色”、选择用完的角色，然后选择“停用”。  

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
如果有兴趣进一步了解 Azure AD Privileged Identity Management，以下链接提供了详细信息。

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png



<!--HONumber=Nov16_HO3-->



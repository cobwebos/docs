---
title: "如何执行访问权限审查 | Microsoft 文档"
description: "了解如何使用 Azure Privileged Identity Management 应用程序执行审查。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 49ee2feb-7d2e-4acf-82c1-40ff23062862
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: a98ed60221eeba1d9c92df846aeae2deafb8ae60
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-perform-an-access-review-in-azure-ad-privileged-identity-management"></a>如何在 Azure AD Privileged Identity Management 中执行访问权限审查
Azure Active Directory (AD) Privileged Identity Management 简化了企业管理以特权身份访问 Azure AD 中的资源和其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）的方式。  

如果已被分配到某个管理角色，组织中的特权角色管理员可能要求定期确认仍然需要使用该角色来完成工作。 你可能会收到一封电子邮件，其中包含一个链接，或者可以直接转[Azure 门户](https://portal.azure.com)。 请遵循本文中的步骤对分配的角色执行自我审查。

如果是特权角色管理员并且想要了解访问权限审查，请参阅[如何开始进行访问权限审查](active-directory-privileged-identity-management-how-to-start-security-review.md)中的详细信息。

## <a name="add-the-privileged-identity-management-application"></a>添加 Privileged Identity Management 应用程序
可以使用 [Azure 门户](https://portal.azure.com/)中的 Azure AD Privileged Identity Management (PIM) 应用程序执行审查。  如果门户中没有 Azure AD Privileged Identity Management 应用程序，请遵循以下步骤开始操作。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择 Azure 门户右上角的用户名，并选择要操作的目录。
3. 选择“更多服务”，并使用“筛选器”文本框搜索“Azure AD Privileged Identity Management”。
4. 选中“固定到仪表板”，并单击“创建”。 Privileged Identity Management 应用程序会打开。

## <a name="approve-or-deny-access"></a>批准或拒绝访问权限
在批准或拒绝访问时，你会只告诉审阅者是否仍或不使用此角色。 如果想要继续充当此角色，请选择“批准”；如果不再需要此访问权限，请选择“拒绝”。 状态不会立即更改，只会在审查人应用结果之后才更改。
请遵循以下步骤来查找并完成访问权限审查：

1. 在 PIM 应用程序中，选择“审查特权访问权限”。 如果有任何挂起的访问权限审查，它们会显示在 Azure AD 的“访问权限审查”边栏选项卡中。
2. 选择想要完成的审查。
3. 除非你将创建评论，显示为评审中的唯一用户。 选中姓名旁边的复选标记。
4. 选择“批准”或“拒绝”。 可能需要在“提供原因”文本框中提供做出该决定的原因。  
5. 关闭“审查 Azure AD 角色”边栏选项卡。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

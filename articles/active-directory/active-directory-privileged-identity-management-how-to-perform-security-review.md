---
title: 如何执行访问权限审查 | Microsoft 文档
description: 了解如何使用 Azure Privileged Identity Management 应用程序执行审查。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f3d8cbdcef25509304785090190f172ae20913b6
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440802"
---
# <a name="how-to-perform-an-access-review-in-azure-ad-privileged-identity-management"></a>如何在 Azure AD Privileged Identity Management 中执行访问权限审查
Azure Active Directory (AD) Privileged Identity Management 简化了企业管理以特权身份访问 Azure AD 中的资源和其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）的方式。  

如果已被分配到某个管理角色，组织中的特权角色管理员可能要求定期确认仍然需要使用该角色来完成工作。 可以通过你所收到的电子邮件中的链接，或者直接转到 [Azure 门户](https://portal.azure.com)来执行此操作。 请遵循本文中的步骤对分配的角色执行自我审查。

若为特权角色管理员或全局管理员，并有意了解访问评审，请参阅[如何开始访问评审](active-directory-privileged-identity-management-how-to-start-security-review.md)，了解详细信息。

## <a name="add-the-privileged-identity-management-application"></a>添加 Privileged Identity Management 应用程序
可以使用 [Azure 门户](https://portal.azure.com/)中的 Azure AD Privileged Identity Management (PIM) 应用程序执行审查。  如果门户中没有 Azure AD Privileged Identity Management 应用程序，请遵循以下步骤开始操作。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择 Azure 门户右上角的用户名，并选择要操作的目录。
3. 选择“所有服务”，并使用“筛选器”文本框搜索“Azure AD Privileged Identity Management”。
4. 选中“固定到仪表板”，并单击“创建”。 Privileged Identity Management 应用程序会打开。

## <a name="approve-or-deny-access"></a>批准或拒绝访问权限
批准或拒绝访问权限时，只是告诉审查人你是否仍要使用此角色。 如果想要继续充当此角色，请选择“批准”；如果不再需要此访问权限，请选择“拒绝”。 状态不会立即更改，只会在审查人应用结果之后才更改。
请遵循以下步骤来查找并完成访问权限审查：

1. 在 PIM 应用程序中，选择“审查特权访问权限”。 如果有任何挂起的访问权限审查，它们会显示在 Azure AD 的“访问权限审查”边栏选项卡中。
2. 选择想要完成的审查。
3. 除非该审查是由你创建的，否则你会显示为该审查中的唯一用户。 选中姓名旁边的复选标记。
4. 选择“批准”或“拒绝”。 可能需要在“提供原因”文本框中提供做出该决定的原因。  
5. 关闭“审查 Azure AD 角色”边栏选项卡。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

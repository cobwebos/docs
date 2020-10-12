---
title: 在 PIM 中评审 Azure AD 角色的访问权限 - Azure AD | Microsoft Docs
description: 了解如何在 Azure Active Directory Privileged Identity Management (PIM) 中评审 Azure AD 角色的访问权限。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 04/24/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd0b63e08f44167b89b6e2d491722f3708b4036
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055429"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中评审对 Azure AD 角色的访问权限

Privileged Identity Management (PIM) 简化了企业对以特权身份访问 Azure Active Directory (AD) 中的资源和其他 Microsoft 联机服务（如 Microsoft 365 或 Microsoft Intune）的行为进行管理的方式。 执行本文中的步骤即可成功地对分配的角色进行自我审查。

如果已被分配到某个管理角色，组织中的特权角色管理员可能要求定期确认仍然需要使用该角色来完成工作。 可以通过所接收电子邮件中的链接或 [Azure 门户](https://portal.azure.com)执行此操作。

若为特权角色管理员或全局管理员，并有意了解访问评审，请参阅[如何开始访问评审](pim-how-to-start-security-review.md)，了解详细信息。

## <a name="add-a-pim-dashboard-tile"></a>添加 PIM 仪表板磁贴

如果未在 Azure 门户中将 Privileged Identity Management 服务固定到仪表板，请按以下步骤开始操作。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择 Azure 门户右上角的用户名，并选择要操作的 Azure AD 组织。
3. 选择“所有服务”，并使用“筛选器”文本框搜索“Azure AD Privileged Identity Management”。
4. 选中“固定到仪表板”，并单击“创建”。 Privileged Identity Management 应用程序会打开。

## <a name="approve-or-deny-access"></a>批准或拒绝访问权限

批准或拒绝访问权限时，只是告诉审查人你是否仍要使用此角色。 如果想要继续充当此角色，请选择“批准”；如果不再需要此访问权限，请选择“拒绝”。  状态不会立即更改，只会在审查人应用结果之后才更改。
请遵循以下步骤来查找并完成访问权限审查：

1. 在 Privileged Identity Management 服务中，选择“审查特权访问”。 如果有任何挂起的访问权限审查，它们会显示在 Azure AD 的“访问权限审查”页中。
2. 选择想要完成的审查。
3. 除非该审查是由你创建的，否则你会显示为该审查中的唯一用户。 选中姓名旁边的复选标记。
4. 选择“批准”或“拒绝”。  可能需要在“提供原因”文本框中提供做出该决定的原因。  
5. 关闭“审查 Azure AD 角色”边栏选项卡。

## <a name="next-steps"></a>后续步骤

- [在 PIM 中对 Azure 资源角色执行访问评审](pim-resource-roles-perform-access-review.md)

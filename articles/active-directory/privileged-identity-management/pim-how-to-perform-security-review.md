---
title: 查看对 PIM 中 Azure AD 角色的访问权限-Azure Active Directory |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management （PIM）中查看 Azure AD 角色的访问权限。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b9f16d6c7f738e64f154506228c832e31e0f5b
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809052"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>查看对 Privileged Identity Management 中 Azure AD 角色的访问权限

Privileged Identity Management （PIM）简化了企业如何管理对 Azure Active Directory （AD）和其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）中资源的特权访问。 请按照本文中的步骤来成功地自动查看分配的角色。

如果已被分配到某个管理角色，组织中的特权角色管理员可能要求定期确认仍然需要使用该角色来完成工作。 你可能会收到一封包含链接的电子邮件，或者你可以直接转到[Azure 门户](https://portal.azure.com)并开始。

若为特权角色管理员或全局管理员，并有意了解访问评审，请参阅[如何开始访问评审](pim-how-to-start-security-review.md)，了解详细信息。

## <a name="add-a-pim-dashboard-tile"></a>添加 PIM 仪表板磁贴

如果未将 Azure AD Privileged Identity Management 服务固定到 Azure 门户中的仪表板，请按照以下步骤开始操作。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择 Azure 门户右上角的用户名，并选择要操作的目录。
3. 选择“所有服务”，并使用“筛选器”文本框搜索“Azure AD Privileged Identity Management”。
4. 选中“固定到仪表板”，并单击“创建”。 Privileged Identity Management 应用程序会打开。

## <a name="approve-or-deny-access"></a>批准或拒绝访问权限

批准或拒绝访问权限时，只是告诉审查人你是否仍要使用此角色。 如果想要继续充当此角色，请选择“批准”；如果不再需要此访问权限，请选择“拒绝”。 状态不会立即更改，只会在审查人应用结果之后才更改。
请遵循以下步骤来查找并完成访问权限审查：

1. 在 Privileged Identity Management 服务中，选择 "**查看特权访问**"。 如果有任何挂起的访问评审，它们将显示在 "Azure AD**访问评审**" 页中。
2. 选择想要完成的审查。
3. 除非该审查是由你创建的，否则你会显示为该审查中的唯一用户。 选中姓名旁边的复选标记。
4. 选择“批准”或“拒绝”。 可能需要在“提供原因”文本框中提供做出该决定的原因。  
5. 关闭“审查 Azure AD 角色”边栏选项卡。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤

- [在 PIM 中对 Azure 资源角色执行访问评审](pim-resource-roles-perform-access-review.md)

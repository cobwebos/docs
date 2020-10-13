---
title: 在 PIM 中使用 Azure 自定义角色 - Azure AD | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中使用 Azure 自定义角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24b7845ec66a85e6ced4f1df9caec409a94016bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88782594"
---
# <a name="use-azure-custom-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中使用 Azure 自定义角色

可能需要对 Azure Active Directory (Azure AD) 组织中具有特权角色的某些用户应用严格的 Privileged Identity Management (PIM) 设置，同时为其他用户提供更大的自主权。 例如，假设你的组织招聘了几名合同工来帮助开发将在 Azure 订阅中运行的应用程序。

作为资源管理员，你希望正式员工可以在不需要审批的情况下获得合格访问权。 但所有合同工在请求访问组织资源时必须接受审批。

按照下一部分中列出的步骤来为 Azure 资源角色设置具针对性的 Privileged Identity Management 设置。

## <a name="create-the-custom-role"></a>创建自定义角色

若要为资源创建自定义角色，请按照 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)中所述的步骤操作。

创建自定义角色后，请提供一个描述性名称，以便可以轻松记住你打算复制的内置角色。

> [!NOTE]
> 请确保自定义角色是需要复制的内置角色的副本，且其作用域与该内置角色匹配。

## <a name="apply-pim-settings"></a>应用 PIM 设置

在 Azure AD 组织中创建角色后，请在 Azure 门户中转到“Privileged Identity Management - Azure 资源”  页。 选择应用该角色的资源。

![“Privileged Identity Management - Azure 资源”窗格](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[配置 Privileged Identity Management 角色设置](pim-resource-roles-configure-role-settings.md)，这些设置应当应用于该角色的这些成员。

最后，为你希望作为这些设置的应用目标的不同成员组[分配角色](pim-resource-roles-assign-roles.md)。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)
- [Azure 中的自定义角色](../../role-based-access-control/custom-roles.md)
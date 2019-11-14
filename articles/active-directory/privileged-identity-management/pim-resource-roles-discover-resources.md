---
title: 发现要在 PIM 中管理的 Azure 资源-Azure AD |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中发现要管理的 Azure 资源。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87da43100c7494937ddc842e0f903ba3a360959e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022891"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>在 Privileged Identity Management 中发现要管理的 Azure 资源

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，可加强对 Azure 资源的保护。 这对于已使用 Privileged Identity Management 来保护 Azure AD 角色，以及希望保护生产资源的管理组和订阅所有者非常有用。

首次设置 Azure 资源的 Privileged Identity Management 时，需要发现并选择要用 Privileged Identity Management 保护的资源。 Privileged Identity Management 可以管理的资源数量没有限制。 但是，我们建议从最重要的（生产）资源开始。

## <a name="discover-resources"></a>发现资源

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure 资源”。

    如果这是你第一次将 Privileged Identity Management 用于 Azure 资源，你会看到 "**发现资源**" 页面。

    ![首次体验时没有列出资源的“发现资源”窗格](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    如果组织中的其他管理员已经在 Privileged Identity Management 管理 Azure 资源，你将看到当前正在管理的资源的列表。

    ![列出当前正在托管的资源的“发现资源”窗格](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. 选择 "**发现资源**" 以启动发现体验。

    ![列出可托管的资源（如订阅和管理组）的“发现”窗格](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. 在 "**发现**" 页上，使用**资源状态筛选**器并**选择 "资源类型**" 以筛选您对其具有写入权限的管理组或订阅。 最初从“所有”开始可能会最简单。

    只能使用 Privileged Identity Management 搜索和选择要管理的管理组或订阅资源。 在 Privileged Identity Management 中管理管理组或订阅时，还可以管理其子资源。

1. 选择要管理的任何非托管资源旁边的复选框。

1. 选择 "**管理资源**" 以开始管理所选资源。

    > [!NOTE]
    > 管理组或订阅一旦被管理，就不能进行非托管。 这会阻止另一个资源管理员删除 Privileged Identity Management 设置。

    ![已选择资源并突出显示“管理资源”选项的“发现”窗格](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. 如果你看到一条消息，确认所选资源的载入进行管理，请选择 **"是"** 。

    ![确认加入所选资源以进行管理的消息](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)

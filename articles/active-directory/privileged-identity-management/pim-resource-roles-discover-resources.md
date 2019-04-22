---
title: 发现要管理 PIM-Azure Active Directory 中的 Azure 资源 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中发现要管理的 Azure 资源。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 786b5ed99cce29c7217bfa42446821848ca8e05f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59492628"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>在 PIM 中发现要管理的 Azure 资源

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，可以提高 Azure 资源的保护。 这非常有用的组织已使用 PIM 保护 Azure AD 角色，并想要保护生产资源的管理组和订阅所有者。

首次为 Azure 资源设置 PIM 时，需要发现并选择要使用 PIM 保护的资源。 可使用 PIM 管理的资源数量没有限制。 但是，我们建议从最重要的（生产）资源开始。

## <a name="discover-resources"></a>发现资源

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure 资源”。

    如果这是首次将 PIM 用于 Azure 资源，则会出现“发现”资源窗格。

    ![发现资源 - 首次](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    如果组织中的另一资源或目录管理员已在 PIM 中管理 Azure 资源，则会出现当前正在托管的资源列表。

    ![“发现资源”窗格](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. 单击“发现资源”以启动发现之旅。

    ![“发现”窗格](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. 在“发现”窗格上，使用“资源状态筛选器”和“选择资源类型”筛选你对其具有写入权限的管理组或订阅。 最初从“所有”开始可能会最简单。

    只能搜索并选择要使用 PIM 管理的管理组或订阅资源。 在 PIM 中管理管理组或订阅时，还可以管理其子资源。

1. 在要管理的任何非托管资源旁添加复选标记。

1. 单击“管理资源”以开始管理所选资源。

    > [!NOTE]
    > 管理组或订阅设为托管后就无法取消托管。 这可防止其他资源管理员删除 PIM 设置。

    ![发现 - 管理资源](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. 如果看到确认加入要管理的所选资源的消息，请单击“是”。

    ![发现 - 管理资源消息](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)
- [在 PIM 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)

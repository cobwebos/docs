---
title: Azure 资源的 Privileged Identity Management - 发现和管理 Azure 资源 | Microsoft Docs
description: 介绍了如何保护 Azure 资源。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 78650e47ec92aa144e4ccc8c57f309240bf31ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="discover-and-manage-azure-resources"></a>发现和管理 Azure 资源

如果你的组织已在使用 Azure AD PIM 保护目录中的管理员，或者你是订阅所有者并且希望保护生产资源，那么你来对地方了！

首次为 Azure 资源启用 PIM 时，需要发现并选择要使用 PIM 保护的资源。 对于可以使用 PIM 管理的资源数没有限制，但是建议先从最重要的（生产）资源开始。

> [!Note]
> 只能搜索并选择订阅资源进行管理。 选择在 PIM 中管理订阅还会启用对所有子资源的管理。

## <a name="discover-resources"></a>发现资源

导航到 Azure AD PIM 并在左侧导航菜单的“管理”部分中选择 Azure 资源。

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

如果这是首次将 PIM 用于 Azure 资源，则需要运行发现来查找要管理的资源。
单击位于屏幕中心的“发现资源”按钮来启动发现体验。

![](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

如果你的组织中的另一位资源或目录管理员已在使用 PIM 管理 Azure 资源，或者针对资源为你分配了合格角色，则列表视图将包含以下消息：“请发现资源或激活合格角色分配以继续”。 

![](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

在操作栏中或在屏幕中央选择此按钮来发现资源后，将会看到可供管理的订阅列表。 此时，如果看到突出显示的订阅，则表明它们受 PIM 保护。

> [!Note]
> 为防止其他资源管理员删除 PIM 设置，一旦某个订阅受管理后，无法将其取消管理。

![](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

将鼠标指针悬停在要使用 PIM 保护的订阅上并选中该行最左侧的框。 一次可以选择多个订阅。

![](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

若要启动加入过程，请选择屏幕顶部的栏中的“管理资源”按钮。

![](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

所选资源现在已由 PIM 管理。 使用页面右上角的“X”关闭发现屏幕，并单击“管理 Azure 资源”屏幕顶部的栏中的“刷新”以开始管理 PIM 设置并分配成员。

![](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>后续步骤

[配置角色设置](pim-resource-roles-configure-role-settings.md)

[在 PIM 中分配角色](pim-resource-roles-assign-roles.md)

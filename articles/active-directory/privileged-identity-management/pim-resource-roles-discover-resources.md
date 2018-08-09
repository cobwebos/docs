---
title: 使用 Privileged Identity Management 发现和管理 Azure 资源 | Microsoft Docs
description: 介绍如何使用 PIM 保护 Azure 资源。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: c5b26c01028e2a5746132939a2058cacdcad859f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622096"
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>使用 Privileged Identity Management 发现和管理 Azure 资源

了解在 Azure Active Directory (Azure AD) 中使用 Privileged Identity Management (PIM) 时，如何发现和管理 Azure 资源。 此信息对于已使用 PIM 保护管理员资源的组织以及希望保护生产资源的订阅所有者非常有用。

首次为 Azure 资源设置 PIM 时，需要发现并选择要使用 PIM 保护的资源。 可使用 PIM 管理的资源数量没有限制。 但是，我们建议从最重要的（生产）资源开始。

> [!NOTE]
> 只能搜索并选择药使用 PIM 管理的订阅资源。 在 PIM 中管理订阅时，还可以管理订阅中的子资源。

## <a name="discover-resources"></a>发现资源

在 Azure 门户中，转到“Privileged Identity Management”窗格。 在左侧菜单的“管理”部分中，选择“Azure 资源”。

![“Privileged Identity Management - Azure 资源”窗格](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

如果这是首次将 PIM 用于 Azure 资源，请先运行发现以查找要管理的资源。 在“发现资源”窗格中，选择“发现资源”按钮以启动发现体验。

![“发现资源”窗格](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

如果组织中的另一位资源管理员或目录管理员已使用 PIM 管理 Azure 资源，或者针对资源为你分配了合格角色，则列表视图将显示消息：“请发现资源或激活合格角色分配以继续”。 

![“Privileged Identity Manager - Azure 资源”窗格中的“发现资源”按钮](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

无论是从顶部菜单还是在窗格中间选择“发现资源”按钮，都会显示出可以管理的订阅列表。 突出显示的订阅已受 PIM 保护。

> [!NOTE]
> 为防止其他资源管理员删除 PIM 设置，将订阅设置为受管理后，便无法将其取消管理。

![“Azure 资源 - 发现”窗格](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

在“资源”列，将鼠标放在要使用 PIM 保护的订阅上。 然后，选择资源名称左侧的复选框。 一次可以选择多个订阅。

![“Azure 资源 - 发现”窗格中的资源列表](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

要启动载入进程，请在顶部菜单中选择“管理资源”。

![“Azure 资源 - 发现”窗格中的“管理资源”按钮](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

所选资源现在已由 PIM 管理。 要关闭发现屏幕，请在右上角选择“X”。要开始管理 PIM 设置并分配成员，请在“Privileged Identity Management - Azure 资源”窗格顶部的菜单中，选择“刷新”按钮。

![“Privileged Identity Management - Azure 资源”窗格顶部菜单中的“刷新”按钮](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>后续步骤

- [配置角色设置](pim-resource-roles-configure-role-settings.md)
- [在 PIM 中分配角色](pim-resource-roles-assign-roles.md)

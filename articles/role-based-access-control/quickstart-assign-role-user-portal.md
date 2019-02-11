---
title: 教程 - 使用 RBAC 和 Azure 门户为用户授予访问权限 | Microsoft Docs
description: 通过在 Azure 门户中分配角色使用基于角色的访问控制 (RBAC) 向用户授予权限。
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.openlocfilehash: 8caa5c3b33ac1b483429251e0c1256636c4ece1a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634466"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-the-azure-portal"></a>教程：使用 RBAC 和 Azure 门户为用户授予访问权限

可以在 Azure 中通过[基于角色的访问控制 (RBAC)](overview.md) 这种方式管理对资源的访问。 在本教程中，你将授权用户在某个资源组中创建和管理虚拟机。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在资源组范围内为用户授予访问权限
> * 删除访问权限

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-resource-group"></a>创建资源组

1. 在导航列表中，选择“资源组”。

1. 选择“添加”以打开“资源组”边栏选项卡。

   ![添加新资源组](./media/quickstart-assign-role-user-portal/resource-group.png)

1. 对于“资源组名称”，输入 **rbac-resource-group**。

1. 选择订阅和位置。

1. 选择“创建”以创建资源组。

1. 选择“刷新”以刷新资源组的列表。

   新资源组将出现在你的资源组列表中。

   ![资源组列表](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>授予访问权限

在 RBAC 中，若要授予访问权限，请创建角色分配。

1. 在“资源组”列表中，选择新的 **rbac-resource-group** 资源组。

1. 选择“访问控制(IAM)”。

1. 选择“角色分配”选项卡以查看当前的角色分配列表。

   ![资源组的“访问控制(IAM)”边栏选项卡](./media/quickstart-assign-role-user-portal/access-control.png)

1. 选择“添加角色分配”以打开“添加角色分配”窗格。

   如果没有分配角色的权限，则将禁用“添加角色分配”选项。

   ![“添加角色分配”窗格](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. 在“角色”下拉列表中，选择“虚拟机参与者”。

1. 在“选择”列表中，选择你自己或其他用户。

1. 选择“保存”，创建角色分配。

   片刻之后，将在 rbac-resource-group 资源组范围内为用户分配“虚拟机参与者”角色。

   ![虚拟机参与者角色分配](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>删除访问权限

在 RBAC 中，若要删除访问权限，请删除角色分配。

1. 在角色分配列表中，在具有“虚拟机参与者”角色的用户旁边添加复选标记。

1. 选择“删除”。

   ![“删除角色分配”消息](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. 在显示的“删除角色分配”消息中，选择“是”。

## <a name="clean-up"></a>清理

1. 在导航列表中，选择“资源组”。

1. 选择 **rbac-resource-group** 以打开该资源组。

1. 选择“删除资源组”以删除该资源组。

   ![删除资源组](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. 在“是否确定要删除”边栏选项卡上，键入资源组名称：**rbac-resource-group**。

1. 选择“删除”以删除该资源组。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 RBAC 和 PowerShell 为用户授予访问权限](tutorial-role-assignments-user-powershell.md)


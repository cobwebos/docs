---
title: Windows 虚拟桌面中的委托访问-Azure
description: 如何在 Windows 虚拟桌面部署上委派管理功能，包括示例。
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2aa2c74704cf89c082d2837b39e82902efa0a62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010049"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Windows 虚拟桌面中的委托访问

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md)。

Windows 虚拟桌面具有委派的访问模型，使你可以通过为特定用户分配角色来定义允许该用户拥有的访问量。 角色分配有三个组件：安全主体、角色定义和作用域。 Windows 虚拟桌面委托访问模型基于 Azure RBAC 模型。 若要详细了解特定的角色分配及其组件，请参阅 [Azure 基于角色的访问控制概述](../role-based-access-control/built-in-roles.md)。

Windows 虚拟桌面委托访问为角色分配的每个元素支持以下值：

* 安全主体
    * 用户
    * 用户组
    * 服务主体
* 角色定义
    * 内置角色
    * 自定义角色
* 范围
    * 主机池
    * 应用组
    * 工作区

## <a name="powershell-cmdlets-for-role-assignments"></a>用于角色分配的 PowerShell cmdlet

在开始之前，请确保按照 [设置 PowerShell 模块](powershell-module.md) 中的说明设置 Windows 虚拟桌面 PowerShell 模块（如果尚未安装）。

在将应用程序组发布到用户或用户组时，Windows 虚拟桌面将使用 Azure 基于角色的访问控制 (Azure RBAC) 。 桌面虚拟化用户角色分配给用户或用户组，而作用域是应用组。 此角色向用户提供对应用程序组的特殊数据访问权限。

运行以下 cmdlet，将 Azure Active Directory 用户添加到应用组：

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

运行以下 cmdlet，将 Azure Active Directory 用户组添加到应用组：

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>后续步骤

有关每个角色可以使用的 PowerShell cmdlet 的更完整列表，请参阅 [powershell 参考](/powershell/windows-virtual-desktop/overview)。

有关 Azure RBAC 中支持的角色的完整列表，请参阅 [azure 内置角色](../role-based-access-control/built-in-roles.md)。

有关如何设置 Windows 虚拟桌面环境的指导，请参阅 [Windows 虚拟桌面环境](environment-setup.md)。

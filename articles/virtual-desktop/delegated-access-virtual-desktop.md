---
title: Windows 虚拟桌面中的委托访问-Azure
description: 如何在 Windows 虚拟桌面部署上委派管理功能，包括示例。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 16b4fca475f91a8cb5b7f9a20ea5aa74b6b674a3
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612854"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Windows 虚拟桌面中的委托访问

>[!IMPORTANT]
>此内容适用于带有 Azure 资源管理器 Windows 虚拟桌面对象的弹簧2020更新。 如果使用的是不带 Azure 资源管理器对象的 Windows 虚拟桌面2019版，请参阅[此文](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md)。
>
> Windows 虚拟桌面春季2020更新目前为公共预览版。 此预览版本在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虚拟桌面具有委派的访问模型，使你可以通过为特定用户分配角色来定义允许该用户拥有的访问量。 角色分配有三个组件：安全主体、角色定义和作用域。 Windows 虚拟桌面委托访问模型基于 Azure RBAC 模型。 若要详细了解特定的角色分配及其组件，请参阅[Azure 基于角色的访问控制概述](../role-based-access-control/built-in-roles.md)。

Windows 虚拟桌面委托访问为角色分配的每个元素支持以下值：

* 安全主体
    * 用户
    * 用户组
    * 服务主体
* 角色定义
    * 内置角色
    * 自定义角色
* 作用域
    * 主机池
    * 应用组
    * 工作区

## <a name="powershell-cmdlets-for-role-assignments"></a>用于角色分配的 PowerShell cmdlet

在开始之前，请确保按照[设置 PowerShell 模块](powershell-module.md)中的说明设置 Windows 虚拟桌面 PowerShell 模块（如果尚未安装）。

在将应用程序组发布到用户或用户组时，Windows 虚拟桌面使用 Azure 基于角色的访问控制（RBAC）。 桌面虚拟化用户角色分配给用户或用户组，而作用域是应用组。 此角色向用户提供对应用程序组的特殊数据访问权限。  

运行以下 cmdlet，将 Azure Active Directory 用户添加到应用组：

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'  
```

运行以下 cmdlet，将 Azure Active Directory 用户组添加到应用组：

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

## <a name="next-steps"></a>后续步骤

有关每个角色可以使用的 PowerShell cmdlet 的更完整列表，请参阅[powershell 参考](/powershell/windows-virtual-desktop/overview)。

有关 Azure RBAC 中支持的角色的完整列表，请参阅[azure 内置角色](../role-based-access-control/built-in-roles.md)。

有关如何设置 Windows 虚拟桌面环境的指导，请参阅[Windows 虚拟桌面环境](environment-setup.md)。

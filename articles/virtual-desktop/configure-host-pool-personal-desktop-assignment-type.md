---
title: Windows 虚拟桌面个人桌面分配类型-Azure
description: 如何为 Windows 虚拟桌面个人桌面主机池配置分配类型。
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8451dc14a7ed42aa92f9adbd5ad050936949e302
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612412"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>配置个人桌面主机池分配类型

>[!IMPORTANT]
>此内容适用于带有 Azure 资源管理器 Windows 虚拟桌面对象的弹簧2020更新。 如果使用的是不带 Azure 资源管理器对象的 Windows 虚拟桌面2019版，请参阅[此文](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md)。
>
> Windows 虚拟桌面春季2020更新目前为公共预览版。 此预览版本在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

你可以配置你的个人桌面主机池的分配类型以调整你的 Windows 虚拟桌面环境，从而更好地满足你的需求。 在本主题中，我们将向你展示如何为用户配置自动或直接分配。

>[!NOTE]
> 本文中的说明仅适用于个人桌面主机池，而不是汇集主机池，因为未将池主机池中的用户分配给特定的会话主机。

## <a name="prerequisites"></a>先决条件

本文假设已下载并安装 Windows 虚拟桌面 PowerShell 模块。 如果尚未安装，请按照[设置 PowerShell 模块](powershell-module.md)中的说明进行操作。

## <a name="configure-automatic-assignment"></a>配置自动分配

自动分配是在 Windows 虚拟桌面环境中创建的新个人桌面主机池的默认分配类型。 自动分配用户不需要特定的会话主机。

若要自动分配用户，请先将其分配给个人桌面主机池，以便他们可以在源中看到桌面。 当已分配的用户在其源中启动桌面时，它们将声明一个可用的会话主机（如果尚未连接到主机池），这会完成分配过程。

若要将主机池配置为自动将用户分配到 Vm，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

若要将用户分配到个人桌面主机池，请运行以下 PowerShell cmdlet：

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>配置直接分配

与自动分配不同，使用直接分配时，必须将用户分配到个人桌面主机池和特定的会话主机，然后才能连接到其个人桌面。 如果仅将用户分配到不具有会话主机分配的主机池，则这些用户将无法访问资源。

若要将主机池配置为需要直接将用户分配到会话主机，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

若要将用户分配到个人桌面主机池，请运行以下 PowerShell cmdlet：

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

若要将用户分配到特定的会话主机，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>后续步骤

配置个人桌面分配类型后，可以登录到 Windows 虚拟桌面客户端，将其作为用户会话的一部分进行测试。 接下来的两个操作方法将告诉你如何使用所选的客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](connect-windows-7-and-10.md)
- [使用 Web 客户端进行连接](connect-web.md)
- [使用 Android 客户端进行连接](connect-android.md)
- [使用 iOS 客户端进行连接](connect-ios.md)
- [使用 macOS 客户端进行连接](connect-macos.md)
---
title: Windows 虚拟桌面个人桌面分配类型 - Azure
description: 如何为 Windows 虚拟桌面个人桌面主机池配置分配类型。
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128287"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>配置个人桌面主机池分配类型

您可以配置个人桌面主机池的分配类型，以调整 Windows 虚拟桌面环境以更好地满足您的需要。 在本主题中，我们将向您展示如何为用户配置自动或直接分配。

>[!NOTE]
> 本文中的说明仅适用于个人桌面主机池，不适用于池主机池，因为池主机池中的用户未分配给特定的会话主机。

## <a name="configure-automatic-assignment"></a>配置自动分配

自动分配是在 Windows 虚拟桌面环境中创建新的个人桌面主机池的默认分配类型。 自动分配用户不需要特定的会话主机。

要自动分配用户，请先将它们分配给个人桌面主机池，以便他们可以在源中看到桌面。 当分配的用户在其源中启动桌面时，如果尚未连接到主机池，他们将声明可用的会话主机，主机池完成分配过程。

在开始之前，如果尚未[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)。 

> [!NOTE]
> 在按照这些说明操作之前，请确保您已安装 Windows 虚拟桌面 PowerShell 模块版本 1.0.1534.2001 或更高版本。

然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

要将主机池配置为自动将用户分配给 VM，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

要将用户分配给个人桌面主机池，请运行以下 PowerShell cmdlet：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>配置直接分配

与自动分配不同，使用直接分配时，必须将用户分配给个人桌面主机池和特定会话主机，然后才能连接到其个人桌面。 如果用户仅分配到没有会话主机分配的主机池，则他们将无法访问资源。

要将主机池配置为需要将用户直接分配给会话主机，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

要将用户分配给个人桌面主机池，请运行以下 PowerShell cmdlet：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

要将用户分配给特定会话主机，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>后续步骤

现在配置了个人桌面分配类型，可以登录到 Windows 虚拟桌面客户端以作为用户会话的一部分对其进行测试。 接下来的两个"如何"将告诉您如何使用您选择的客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](connect-windows-7-and-10.md)
- [使用 Web 客户端进行连接](connect-web.md)

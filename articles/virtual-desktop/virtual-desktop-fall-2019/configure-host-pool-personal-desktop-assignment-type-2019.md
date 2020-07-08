---
title: Windows 虚拟桌面个人桌面分配类型 - Azure
description: 如何为 Windows 虚拟桌面个人桌面主机池配置分配类型。
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 376f9d78f508c245bec2d066863566ace68eff0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85204857"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>配置个人桌面主机池分配类型

>[!IMPORTANT]
>本教程的内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的 2019 年秋季版。 如果要尝试管理 2020 年春季版更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../configure-host-pool-personal-desktop-assignment-type.md)。

你可以配置个人桌面主机池的分配类型，以调整 Windows 虚拟桌面环境来更好地满足你的需求。 在本主题中，我们将介绍如何为用户配置自动分配或直接分配。

>[!NOTE]
> 本文中的说明仅适用于个人桌面主机池而不适用于共用主机池，因为不会将共用主机池中的用户分配到特定的会话主机。

## <a name="configure-automatic-assignment"></a>配置自动分配

自动分配是 Windows 虚拟桌面环境中新创建的个人桌面主机池的默认分配类型。 自动分配用户不需要特定的会话主机。

若要自动分配用户，请先将其分配到个人桌面主机池，以便他们能够在其源中看到桌面。 当分配的用户在其源中启动桌面时，如果他们尚未连接到主机池，则他们将认领一个可用的会话主机，分配过程就此完成。

在开始之前，请[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做）。

> [!NOTE]
> 在按照下面的说明操作之前，请确保已安装 Windows 虚拟桌面 PowerShell 模块版本 1.0.1534.2001 或更高版本。

然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

若要将主机池配置为自动将用户分配到 VM，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

若要将用户分配到个人桌面主机池，请运行以下 PowerShell cmdlet：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>配置直接分配

与自动分配不同，使用直接分配时，必须将用户分配到个人桌面主机池和特定的会话主机，然后他们才能连接到其个人桌面。 如果仅将用户分配到主机池而不分配会话主机，则他们将无法访问资源。

若要将主机池配置为要求直接将用户分配到会话主机，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

若要将用户分配到个人桌面主机池，请运行以下 PowerShell cmdlet：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

若要将用户分配到特定的会话主机，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="remove-a-user-assignment"></a>删除用户分配

你可能会出于各种原因而希望删除用户分配，例如：用户不再需要个人桌面、用户已离职，或者你要将桌面重新用于其他人。

目前，为个人桌面删除用户分配的唯一方法是完全删除会话主机。 若要删除会话主机，请运行以下 cmdlet：

```powershell
Remove-RdsSessionHost
```

如果需要将会话主机重新添加到个人桌面主机池，请在该计算机上卸载 Windows 虚拟桌面，然后按照[使用 PowerShell 创建主机池](create-host-pools-powershell-2019.md)中的步骤重新注册会话主机。

## <a name="next-steps"></a>后续步骤

配置个人桌面分配类型后，可以登录到 Windows 虚拟桌面客户端，将其作为用户会话的一部分进行测试。 接下来的两篇操作指南将会介绍如何使用所选的客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](../connect-windows-7-and-10.md)
- [使用 Web 客户端进行连接](connect-web-2019.md)

---
title: 自定义 Windows 虚拟桌面用户的源 - Azure
description: 如何使用 PowerShell cmdlet 自定义 Windows 虚拟桌面用户的源。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128081"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>为 Windows 虚拟桌面用户自定义源

您可以自定义源，以便远程应用和远程桌面资源以可识别的方式显示给用户。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>自定义远程应用的显示名称

您可以通过设置友好名称来更改已发布的 RemoteApp 的显示名称。 默认情况下，友好名称与 RemoteApp 程序的名称相同。

要检索应用组的已发布远程应用列表，请运行以下 PowerShell cmdlet：

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![突出显示了 PowerShell cmdlet get-RDSRemoteApp 的屏幕截图，突出显示了名称和友好名称。](media/get-rdsremoteapp.png)

要将友好名称分配给 RemoteApp，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![突出显示了 PowerShell cmdlet Set-RDSRemoteApp 的屏幕截图，其中突出显示了名称和新的友好名称。](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>自定义远程桌面的显示名称

您可以通过设置友好名称来更改已发布的远程桌面的显示名称。 如果通过 PowerShell 手动创建主机池和桌面应用组，则默认友好名称为"会话桌面"。 如果通过 GitHub Azure 资源管理器模板或 Azure 应用商店产品库创建了主机池和桌面应用组，则默认友好名称与主机池名称相同。

要检索远程桌面资源，请运行以下 PowerShell cmdlet：

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![突出显示了 PowerShell cmdlet get-RDSRemoteApp 的屏幕截图，突出显示了名称和友好名称。](media/get-rdsremotedesktop.png)

要为远程桌面资源分配友好名称，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![突出显示了 PowerShell cmdlet Set-RDSRemoteApp 的屏幕截图，其中突出显示了名称和新的友好名称。](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>后续步骤

现在，您已为用户自定义了源，您可以登录到 Windows 虚拟桌面客户端以将其测试出来。为此，请继续连接到 Windows 虚拟桌面操作：
    
 * [从 Windows 10 或 Windows 7 进行连接](connect-windows-7-and-10.md)
 * [从 Web 浏览器进行连接](connect-web.md) 

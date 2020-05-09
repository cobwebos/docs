---
title: 自定义 Windows 虚拟桌面用户的源-Azure
description: 如何通过 PowerShell cmdlet 为 Windows 虚拟桌面用户自定义源。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a93aa35353940cfdbded1634448d4f6d2865c365
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614832"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>为 Windows 虚拟桌面用户自定义源

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。 如果尝试管理春季2020更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../customize-feed-for-virtual-desktop-users.md)。

你可以自定义该源，使 RemoteApp 和远程桌面资源以可识别的方式显示给用户。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>自定义 RemoteApp 的显示名称

可以通过设置友好名称来更改已发布的 RemoteApp 的显示名称。 默认情况下，友好名称与 RemoteApp 程序的名称相同。

若要检索应用组的已发布 RemoteApps 的列表，请运行以下 PowerShell cmdlet：

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![已突出显示 Name 和 FriendlyName 的 PowerShell cmdlet RDSRemoteApp 的屏幕截图。](../media/get-rdsremoteapp.png)

若要为 RemoteApp 分配一个友好名称，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![已突出显示名称和新 FriendlyName 的 PowerShell cmdlet RDSRemoteApp 的屏幕截图。](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>自定义远程桌面的显示名称

可以通过设置友好名称来更改已发布远程桌面的显示名称。 如果通过 PowerShell 手动创建了主机池和桌面应用组，则默认友好名称为 "会话桌面"。 如果通过 GitHub Azure 资源管理器模板或 Azure Marketplace 产品创建了主机池和桌面应用组，则默认的友好名称与主机池名称相同。

若要检索远程桌面资源，请运行以下 PowerShell cmdlet：

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![已突出显示 Name 和 FriendlyName 的 PowerShell cmdlet RDSRemoteApp 的屏幕截图。](../media/get-rdsremotedesktop.png)

若要为远程桌面资源分配一个友好名称，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![已突出显示名称和新 FriendlyName 的 PowerShell cmdlet RDSRemoteApp 的屏幕截图。](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>后续步骤

自定义用户的源后，可以登录到 Windows 虚拟桌面客户端进行测试。为此，请继续阅读连接到 Windows 虚拟桌面的操作指南：
    
 * [从 Windows 10 或 Windows 7 进行连接](../connect-windows-7-and-10.md)
 * [从 Web 浏览器进行连接](connect-web-2019.md) 

---
title: 自定义 Windows 虚拟桌面用户的 Azure 源
description: 如何自定义 Windows 虚拟桌面用户使用 PowerShell cmdlet 的源。
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: v-hevem
ms.openlocfilehash: 5fe2a8b8ee5870ff7986ca2d91739f82a5128882
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618990"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>为 Windows 虚拟桌面用户自定义源

因此 RemoteApp 和远程桌面资源会显示为用户识别的方式，可以自定义源。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)（如果尚未这样做），以便在 PowerShell 会话中使用。

## <a name="customize-the-display-name-for-a-remoteapp"></a>自定义 RemoteApp 的显示名称

可以通过设置的友好名称来更改已发布的 RemoteApp 的显示名称。 默认情况下，友好名称为 RemoteApp 程序的名称相同。

若要检索已发布的 RemoteApps 的应用程序组的列表，请运行以下 PowerShell cmdlet:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![PowerShell cmdlet 的屏幕截图，突出显示 Get RDSRemoteApp 具有名称和友好名称。](media/get-rdsremoteapp.png)

若要将友好名称分配到 RemoteApp，请运行以下 PowerShell cmdlet:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![PowerShell cmdlet 集 RDSRemoteApp 具有名称和新 FriendlyName 突出显示的屏幕截图。](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>自定义远程桌面的显示名称

可以通过设置的友好名称来更改已发布的远程桌面的显示名称。 如果池和桌面应用程序组通过 PowerShell 手动创建主机，默认的友好名称是"桌面连接会话。" 如果池和桌面应用程序组通过 GitHub Azure 资源管理器模板或 Azure Marketplace 产品/服务创建主机，默认的友好名称是与主机池的名称相同。

若要检索远程桌面资源，请运行以下 PowerShell cmdlet:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![PowerShell cmdlet 的屏幕截图，突出显示 Get RDSRemoteApp 具有名称和友好名称。](media/get-rdsremotedesktop.png)

若要为远程桌面资源分配友好名称，运行以下 PowerShell cmdlet:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![PowerShell cmdlet 集 RDSRemoteApp 具有名称和新 FriendlyName 突出显示的屏幕截图。](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>后续步骤

现在，你已自定义用户源，您可以登录到 Windows 虚拟桌面客户端进行测试。为此，请转到连接到 Windows 虚拟桌面操作方法：
    
 * [从 Windows 10 或 Windows 7 进行连接](connect-windows-7-and-10.md)
 * [从 Web 浏览器进行连接](connect-web.md) 

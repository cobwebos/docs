---
title: 自定义 Windows 虚拟桌面用户的源-Azure
description: 如何通过 PowerShell cmdlet 为 Windows 虚拟桌面用户自定义源。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 961fadfff0147d8c5258fa5acf31d8b0649ea12a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612888"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>为 Windows 虚拟桌面用户自定义源

>[!IMPORTANT]
>此内容适用于带有 Azure 资源管理器 Windows 虚拟桌面对象的弹簧2020更新。 如果使用的是不带 Azure 资源管理器对象的 Windows 虚拟桌面2019版，请参阅[此文](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md)。
>
> Windows 虚拟桌面春季2020更新目前为公共预览版。 此预览版本在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

你可以自定义该源，使 RemoteApp 和远程桌面资源以可识别的方式显示给用户。

## <a name="prerequisites"></a>先决条件

本文假设已下载并安装 Windows 虚拟桌面 PowerShell 模块。 如果尚未安装，请按照[设置 PowerShell 模块](powershell-module.md)中的说明进行操作。

## <a name="customize-the-display-name-for-a-remoteapp"></a>自定义 RemoteApp 的显示名称

可以通过设置友好名称来更改已发布的 RemoteApp 的显示名称。 默认情况下，友好名称与 RemoteApp 程序的名称相同。

若要检索应用组的已发布 RemoteApps 的列表，请运行以下 PowerShell cmdlet：

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

若要为 RemoteApp 分配一个友好名称，请使用所需的参数运行以下 cmdlet：

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

例如，假设您检索了当前应用程序并带有以下示例 cmdlet：

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

输出如下所示：

```powershell
CommandLineArgument : 
CommandLineSetting  : DoNotAllow 
Description         : 
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe 
FriendlyName        : Microsoft Word 
IconContent         : {0, 0, 1, 0…} 
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64 
IconIndex           : 0 
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe 
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word 
Name                : 0301RAG/Microsoft Word 
ShowInPortal        : False 
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications 
```
若要更新友好名称，请运行以下 cmdlet：

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe" 
```

若要确认已成功更新友好名称，请运行以下 cmdlet：

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName 
```

Cmdlet 应为你生成以下输出：

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>自定义远程桌面的显示名称

可以通过设置友好名称来更改已发布远程桌面的显示名称。 如果通过 PowerShell 手动创建了主机池和桌面应用组，则默认友好名称为 "会话桌面"。 如果通过 GitHub Azure 资源管理器模板或 Azure Marketplace 产品创建了主机池和桌面应用组，则默认的友好名称与主机池名称相同。

若要检索远程桌面资源，请运行以下 PowerShell cmdlet：

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

若要为远程桌面资源分配一个友好名称，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>在 Azure 门户中自定义显示名称

可以通过使用 Azure 门户设置友好名称来更改已发布远程桌面的显示名称。 

1. 通过 <https://portal.azure.com> 登录到 Azure 门户。 

2. 搜索**Windows 虚拟桌面**。

3. 在 "服务" 下，选择 " **Windows 虚拟桌面**"。 

4. 在 Windows 虚拟桌面页面上，选择屏幕左侧的 "**应用程序组**"，然后选择要编辑的应用组的名称。 

5. 在屏幕左侧的菜单中选择 "**应用程序**"。

6. 选择要更新的应用程序，然后输入新的**显示名称**。 

7. 选择“保存”  。 你编辑的应用程序现在应显示更新的名称。

## <a name="next-steps"></a>后续步骤

自定义用户的源后，可以登录到 Windows 虚拟桌面客户端进行测试。为此，请继续阅读连接到 Windows 虚拟桌面的操作指南：
    
 * [与 Windows 10 或 Windows 7 连接](connect-windows-7-and-10.md)
 * [使用 Web 客户端进行连接](connect-web.md) 
 * [使用 Android 客户端进行连接](connect-android.md)
 * [使用 iOS 客户端进行连接](connect-ios.md)
 * [使用 macOS 客户端进行连接](connect-macos.md)

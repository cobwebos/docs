---
title: 管理 Windows 虚拟桌面预览版的应用组 - Azure
description: 介绍如何在 Azure Active Directory 中设置 Windows 虚拟桌面预览版租户。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: f0cdd28be8c6e7390aa26fdc2dfbf32ec5542c2d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233909"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>教程：管理 Windows 虚拟桌面预览版的应用组

为新的 Windows 虚拟桌面预览版主机池创建的默认应用组也会发布完整桌面。 此外，可为主机池创建一个或多个 RemoteApp 应用程序组。 请遵循本教程创建 RemoteApp 应用组并发布单个“开始”菜单应用。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 RemoteApp 组。
> * 授予对 RemoteApp 的访问权限

在开始之前，如果尚未这样做，请[下载并导入 Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)（Windows 虚拟桌面模块），以便在 PowerShell 会话中使用。

## <a name="create-a-remoteapp-group"></a>创建 RemoteApp 组

1. 运行以下 PowerShell cmdlet 以创建新的空 RemoteApp 应用组。

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. （可选）若要验证是否已创建应用组，可运行以下 cmdlet，以查看主机池的所有应用组列表。

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. 运行以下 cmdlet，以获取主机池虚拟机映像中的开始菜单应用列表。 记下要发布的应用程序的 **FilePath**、**IconPath**、**IconIndex** 值和其他重要信息。

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. 运行以下 cmdlet，基于其 appalias 安装应用程序。 运行步骤 3 的输出时，可以看到 appalias。

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. （可选）运行以下 cmdlet，将新的 RemoteApp 发布到在步骤 1 中创建的应用程序组。

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. 若要验证应用是否已发布，请运行以下 cmdlet。

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. 针对要为此应用组发布的每个应用程序重复步骤 1-5。
8. 运行以下 cmdlet，授予用户对应用组中 RemoteApp 的访问权限。

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>后续步骤

在本教程中，学习了如何创建应用组、使用 RemoteApps 来填充它，还学习了如何向应用组分配用户。 若要详细了解如何登录到 Windows 虚拟桌面，请继续学习“连接到 Windows 虚拟桌面”操作指南。

- [连接到 Windows 7 和 Windows 10 上的远程桌面客户端](connect-windows-7-and-10.md)
- [连接到 Windows 虚拟桌面预览版 Web 客户端](connect-web.md)

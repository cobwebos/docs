---
title: 管理 Windows 虚拟桌面的应用组 - Azure
description: 介绍如何在 Azure Active Directory 中设置 Windows 虚拟桌面租户。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 55c2ed5f9066d85643588f77806bbd41e92dd16b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82613943"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>教程：管理 Windows 虚拟桌面的应用组

>[!IMPORTANT]
>本教程的内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的 2019 年秋季版。 如果要尝试管理 2020 年春季版更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../manage-app-groups.md)。

为新的 Windows 虚拟桌面主机池创建的默认应用组也会发布完整桌面。 此外，可为主机池创建一个或多个 RemoteApp 应用程序组。 请遵循本教程创建 RemoteApp 应用组并发布单个“开始”  菜单应用。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 RemoteApp 组。
> * 授予对 RemoteApp 程序的访问权限。

在开始之前，如果尚未这样做，请[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)，以便在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>创建 RemoteApp 组

1. 运行以下 PowerShell cmdlet 以创建新的空 RemoteApp 应用组。

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. （可选）若要验证是否已创建应用组，可运行以下 cmdlet，以查看主机池的所有应用组列表。

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. 运行以下 cmdlet，以获取主机池虚拟机映像中的“开始”  菜单应用列表。 记下要发布的应用程序的 **FilePath**、**IconPath**、**IconIndex** 值和其他重要信息。

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. 运行以下 cmdlet，以基于其 `AppAlias` 安装应用程序。 运行步骤 3 的输出时，可以看到 `AppAlias`。

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. （可选）运行以下 cmdlet，将新的 RemoteApp 程序发布到在步骤 1 中创建的应用程序组。

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. 若要验证应用是否已发布，请运行以下 cmdlet。

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. 针对要为此应用组发布的每个应用程序重复步骤 1-5。
8. 运行以下 cmdlet，授予用户对应用组中 RemoteApp 程序的访问权限。

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建应用组、使用 RemoteApps 程序来填充它，以及如何向应用组分配用户。 若要了解如何创建验证主机池，请参阅以下教程。 在将服务更新部署到生产环境之前，可以使用验证主机池来监视服务更新。

> [!div class="nextstepaction"]
> [创建主机池以验证服务更新](create-validation-host-pool-2019.md)

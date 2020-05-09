---
title: 在 Windows 虚拟桌面中发布内置应用程序-Azure
description: 如何在 Windows 虚拟桌面中发布内置应用。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a02bf514ff76f5528bc46f0a60642163c9278ebf
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615131"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>在 Windows 虚拟桌面中发布内置应用

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。 如果尝试管理春季2020更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../publish-apps.md)。

本文将介绍如何在 Windows 虚拟桌面环境中发布应用。

## <a name="publish-built-in-apps"></a>发布内置应用

发布内置应用程序：

1. 连接到主机池中的某个虚拟机。
2. 按照[本文](/powershell/module/appx/get-appxpackage?view=win10-ps/)中的说明获取要发布的应用的**PackageFamilyName** 。
3. 最后，运行以下 cmdlet，并`<PackageFamilyName>`将其替换为在上一步中找到的**PackageFamilyName** ：
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows 虚拟桌面仅支持通过以开头的安装位置发布应用`C:\Program Files\Windows Apps`。

## <a name="update-app-icons"></a>更新应用图标

发布应用后，它将具有默认的 Windows 应用图标，而不是其常规图标图片。 若要将图标更改为其常规图标，请将所需的图标的图像放到网络共享上。 支持的图像格式为 PNG、BMP、GIF、JPG、JPEG 和 .ICO。

## <a name="publish-microsoft-edge"></a>发布 Microsoft Edge

用于发布 Microsoft Edge 的过程与其他应用程序的发布过程稍有不同。 若要将 Microsoft Edge 与默认主页一起发布，请运行以下 cmdlet：

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>后续步骤

- 了解如何配置源，以组织为[Windows 虚拟桌面用户自定义源](customize-feed-virtual-desktop-users-2019.md)中的用户显示应用程序的方式。
- 了解[设置 .msix 应用附加](../app-attach.md)的 .msix 应用附加功能。


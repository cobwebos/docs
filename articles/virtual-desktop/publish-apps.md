---
title: 在 Windows 虚拟桌面中发布内置应用 - Azure
description: 如何在 Windows 虚拟桌面中发布内置应用。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127736"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>在 Windows 虚拟桌面中发布内置应用

本文将介绍如何在 Windows 虚拟桌面环境中发布应用。

## <a name="publish-built-in-apps"></a>发布内置应用

要发布内置应用，可以：

1. 连接到主机池中的虚拟机之一。
2. 按照[本文](/powershell/module/appx/get-appxpackage?view=win10-ps/)中的说明获取要发布的应用程序的**包家庭名称**。
3. 最后，运行以下 cmdlet，`<PackageFamilyName>`替换为您在上一步中找到的**包家庭名称**：
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows 虚拟桌面仅支持发布安装位置以`C:\Program Files\Windows Apps`开始的应用。

## <a name="update-app-icons"></a>更新应用图标

发布应用后，它将具有默认的 Windows 应用图标，而不是其常规图标图片。 要将图标更改为其常规图标，可以将所需的图标图像放在网络共享上。 支持的图像格式包括 PNG、BMP、GIF、JPG、JPEG 和 ICO。

## <a name="publish-microsoft-edge"></a>发布微软边缘

用于发布 Microsoft Edge 的过程与其他应用的发布过程略有不同。 要使用默认主页发布 Microsoft Edge，请运行此 cmdlet：

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>后续步骤

- 了解如何配置源以在[Windows 虚拟桌面用户自定义源](customize-feed-for-virtual-desktop-users.md)中组织如何为用户显示应用。
- 在[设置 MSIX 应用附加](app-attach.md)时了解有关 MSIX 应用附加功能。


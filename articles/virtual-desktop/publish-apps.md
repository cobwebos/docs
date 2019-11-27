---
title: 在 Windows 虚拟桌面中发布内置应用程序-Azure
description: 如何在 Windows 虚拟桌面中发布新式应用。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: helohr
ms.openlocfilehash: 26ebcc7f2196267719a5f49aeeb8de00d48b9c3e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483731"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>在 Windows 虚拟桌面中发布内置应用

本文将介绍如何在 Windows 虚拟桌面环境中发布应用。

## <a name="publish-built-in-apps"></a>发布内置应用

发布内置应用程序：

1. 连接到主机池中的某个虚拟机。
2. 按照[本文](https://docs.microsoft.com/powershell/module/appx/get-appxpackage?view=win10-ps)中的说明获取要发布的应用的**PackageFamilyName** 。
3. 最后，运行以下 cmdlet，`<PackageFamilyName>` 替换为在上一步中找到的**PackageFamilyName** ：
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows 虚拟桌面仅支持通过以 `C:\Program Files\Windows Apps`开头的安装位置发布应用。

## <a name="update-app-icons"></a>更新应用图标

发布应用后，它将具有默认的 Windows 应用图标，而不是其常规图标图片。 若要将图标更改为其常规图标，请将所需的图标的图像放到网络共享上。 支持的图像格式为 PNG、BMP、GIF、JPG、JPEG 和 .ICO。

## <a name="publish-microsoft-edge"></a>发布 Microsoft Edge

用于发布 Microsoft Edge 的过程与其他应用程序的发布过程稍有不同。 若要将 Microsoft Edge 与默认主页一起发布，请运行以下 cmdlet：

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```




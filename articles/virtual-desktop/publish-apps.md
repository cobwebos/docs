---
title: 在 Windows 虚拟桌面中发布内置应用程序-Azure
description: 如何在 Windows 虚拟桌面中发布内置应用。
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b088f1f58e26dca854c17b0765607ebe76017dfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009488"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>在 Windows 虚拟桌面中发布内置应用

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/publish-apps-2019.md)。

本文将介绍如何在 Windows 虚拟桌面环境中发布应用。

## <a name="publish-built-in-apps"></a>发布内置应用

发布内置应用程序：

1. 连接到主机池中的某个虚拟机。
2. 按照[本文](/powershell/module/appx/get-appxpackage?view=win10-ps/)中的说明获取要发布的应用的**PackageFamilyName** 。
3. 最后，运行以下 cmdlet，并将其 `<PackageFamilyName>` 替换为在上一步中找到的 **PackageFamilyName** ：

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> Windows 虚拟桌面仅支持通过以开头的安装位置发布应用 `C:\Program Files\WindowsApps` 。

## <a name="update-app-icons"></a>更新应用图标

发布应用后，它将具有默认的 Windows 应用图标，而不是其常规图标图片。 若要将图标更改为其常规图标，请将所需的图标的图像放到网络共享上。 支持的图像格式为 PNG、BMP、GIF、JPG、JPEG 和 .ICO。

## <a name="publish-microsoft-edge"></a>发布 Microsoft Edge

用于发布 Microsoft Edge 的过程与其他应用程序的发布过程稍有不同。 若要将 Microsoft Edge 与默认主页一起发布，请运行以下 cmdlet：

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>后续步骤

- 了解如何配置源，以组织为 [Windows 虚拟桌面用户自定义源](customize-feed-for-virtual-desktop-users.md)中的用户显示应用程序的方式。
- 了解 [设置 .msix 应用附加](app-attach.md)的 .msix 应用附加功能。


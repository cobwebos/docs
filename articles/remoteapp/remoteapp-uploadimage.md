---
title: "上传 Azure RemoteApp 的自定义映像 | Microsoft Docs"
description: "了解如何上载 Azure RemoteApp 的自定义映像"
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: 299e0510-1a6b-4fdf-914a-3631b061a360
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: ericor
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: dcf897cfb03316312613a641f1758cd4636d06b7
ms.lasthandoff: 03/31/2017


---
# <a name="upload-a-custom-image-for-azure-remoteapp"></a>上载 Azure RemoteApp 的自定义映像
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

现在你已创建你的自定义模板映像或者已更新映像的更改，你需要将该映像上载到你的 Azure RemoteApp 映像库。 执行以下步骤。

## <a name="before-you-start"></a>开始之前
1. 验证你的自定义映像是否满足[映像要求](remoteapp-imagereqs.md)和[应用程序要求](remoteapp-appreqs.md)。
2. 安装 [Azure PowerShell 模块](/powershell/azureps-cmdlets-docs)。

## <a name="step-by-step-on-how-to-upload-custom-image"></a>有关如何上传自定义映像的分步指导
1. 打开“Azure 管理门户”，导航到“RemoteApp”页面。
2. 在“**模板映像**”选项卡上，单击位于页面底部的“**上载**”。
3. 输入映像的一个友好名称，然后指定存储帐户位置。 请确保该位置与你的 RemoteApp 集合位置相同，或者就是你想要创建的位置。
4. 出现提示时，将脚本下载到本地 PC。
5. 将文本框中的命令参数复制到剪贴板。
6. 打开已提升的 Windows PowerShell 窗口。
7. 从提升的 Windows PowerShell 窗口中，导航到下载脚本的同一个目录。
8. 粘贴复制的命令，然后按 **Enter** 键。
   
   上载过程将开始，持续时间可能取决于许多因素，包括你的网速和映像的大小
9. 如果由于网络中断或类似事项你的上载未成功，你始终可以继续已开始的上载进程。 若要继续上载，请用相同的命令行再次运行脚本。

> [!WARNING]
> 永远不要修改上载脚本。 已实施特定检查，以确保映像满足映像要求和应用程序要求。
> 
> 

## <a name="common-problems"></a>常见问题
* 请确保使用 Windows PowerShell 而不是 Azure PowerShell。 你需要安装 Azure PowerShell 模块，因为在上载过程中会需要某些模块。
* 永远不要更改脚本，验证可以在你方便的时候进行。
* 如果 vhd 文件在上载过程中被锁定，请复制该文件或将它移至一个新位置并再次尝试上载。 可能存在某些阻止上载的 Windows 进程。  



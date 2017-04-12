---
title: "Azure RemoteApp 映像要求 | Microsoft Docs"
description: "了解创建与 Azure RemoteApp 一起使用的映像的要求"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 7cbb90f4-6dc9-462c-a429-088cdb57414e
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 75b0f8d6b25a80f11002b683152cfb294cbb68bd
ms.lasthandoff: 03/31/2017


---
# <a name="requirements-for-azure-remoteapp-images"></a>Azure RemoteApp 映像的要求
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

Azure RemoteApp 使用 Windows Server 2012 R2 映像托管要与用户共享的所有项目。 若要创建自定义映像，可以由现有映像开始或者[新建一个映像](remoteapp-create-custom-image.md)。

> [!TIP]
> 你是否知道，通过你的 Azure RemoteApp 订阅，你可以访问 Azure VM 库中的 Windows Server 2012 R2 映像，而该映像可用于创建你自己的模板映像？ [请查看](remoteapp-image-on-azurevm.md)。  
> 
> 

可以上载和 Azure RemoteApp 一起使用的映像要求为：

* 自定义应用程序不会在映像上本地存储数据。 这些映像是无状态的，并且应仅包含应用程序。
* 映像不包含可能会丢失的数据。
* 映像大小应是 MB 的倍数。 如果尝试上载的映像大小不是准确的倍数，则上载会失败。
* 映像大小必须为 127 GB 或更小。
* 其必须位于 VHD 文件（当前不支持 VHDX 文件）上。
* VHD 不能为第 2 代虚拟机。
* VHD 可以是固定大小或可动态扩展的。 建议使用动态扩展的 VHD，因为其上载到 Azure 所需的时间少于固定大小的 VHD 文件所需的时间。
* 磁盘必须使用主启动记录 (MBR) 分区类型初始化。 不支持 GUID 分区表 (GPT) 分区类型。
* VHD 必须包含 Windows Server 2012 R2 的单个安装程序。 其可以包含多个卷，但仅能包含一个含有 Windows 安装程序的卷。
* 必须安装远程桌面会话主机 (RDSH) 角色和桌面体验功能。
* 一定*不要*安装远程桌面连接代理角色。
* 必须禁用加密文件系统 (EFS)。
* 映像必须使用参数 **/oobe /generalize /shutdown** 进行 sysprep（请勿使用 **/mode:vm** 参数）。
* 不支持上载快照链中的 VHD。

有关为 Azure RemoteApp 创建映像的详细信息，请参阅[创建 Azure RemoteApp 映像](remoteapp-imageoptions.md)。



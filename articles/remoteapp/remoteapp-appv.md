---
title: "配合使用 App-V 应用和 Azure RemoteApp | Microsoft Docs"
description: "了解如何在 Azure RemoteApp 中使用 APP-V 应用。"
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: e2292cb2-5c89-4b2b-ab11-74dbacd07c31
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8057e9ce27f9df3e9455d48d9c8196033612c8f3


---
# <a name="using-app-v-apps-in-azure-remoteapp"></a>在 Azure RemoteApp 中使用 App-V 应用
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

你可以在需要加入域的 Azure RemoteApp 混合集合中使用 App-V 应用程序。

在开始之前，请确保使用最新的更新程序安装 App-V 5.1 客户端。 你将需要创建包括 App-V 客户端的[自定义映像](remoteapp-create-custom-image.md)。  

将现有 App-V 基础结构与 Azure RemoteApp 一起使用很容易。 由于混合集合是部署到对你的域控制器具有访问权限的 Azure VNET，并且 VM 已加入域，因此，你可以利用现有 App-V 基础结构和部署方法，轻松在 Azure RemoteApp 中托管 App-V 应用程序。 下面是根据当前具有的 App-V 部署类型，应注意的一些事项：

| 配置选项 |  | 正 | 负 |
| --- | --- | --- | --- |
| 传递方法 |流式处理（按需） |应用始终是最新和全新的 |第一次延迟 |
| 已装入 |最快；应用已存在于 VM 上 |膨胀 - 占用映像空间（限值为 127 GB） | |
| 应用位置存储 |共享内容 |在 Azure RemoteApp 实例的内存中运行的应用 |会消耗内存和影响到应用所在的流式处理（文件）服务器的良好连接 |
| 磁盘（已缓存） |快速执行。 应用不依赖于内容源的可用性 |膨胀 - 占用映像空间（限值为 127 GB） | |
| 目标 |用户 |需要完整独立的 App-V 基础结构 | |
| 全局（计算机） |预发布或使用发布服务器作为目标 |如果你想要更新应用（大型），则需要更新 Azure 映像。 占用映像上的一些空间。 | |

 创建自定义映像和混合集合后，发布你的应用程序，分配用户并尽享托管在传递到任何设备的任意位置的 Azure RemoteApp 中的现有 App-V 应用程序。




<!--HONumber=Nov16_HO3-->



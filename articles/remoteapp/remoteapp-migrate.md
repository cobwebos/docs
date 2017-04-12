---
title: "在 Azure RemoteApp 中迁移用户数据 | Microsoft Docs"
description: "了解如何将用户数据迁入和迁出 Azure RemoteApp。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d7e4fbf1-cb42-4430-94a0-ed6d4676fc86
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 39dd726078c4dcc55063300bdca998ad822feadd
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>如何将用户数据迁入和迁出 Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

可以使用多种不同工具和方法将 [用户数据](remoteapp-upd.md) 迁入和迁出 Azure RemoteApp。 以下是一些方法：

* 使用剪切板共享复制和粘贴
* 将文件和数据复制到文件服务器
* 通过浏览器将文件复制到 OneDrive for Business
* 使用重定向复制文件

> [!NOTE]
> 无法启用 OneDrive for Business 或消费者同步代理 - 他们在 [Azure RemoteApp](remoteapp-onedrive.md) 中不受支持。
> 
> 

## <a name="use-copy-and-paste-in-file-explorer"></a>在文件资源管理器中使用复制和粘贴
已 [默认](remoteapp-redirection.md) 在 RemoteApp 部署中启用使用剪贴板复制和粘贴。 这使用户可以在本地 PC 和 RemoteApp 应用间复制文件。 通常，通过使用 RemoteApp 中的应用的正常流程，用户已将文件保存到它们的 UPD - 将此数据移除 RemoteApp 很简单：

1. [将文件资源管理器发布为 RemoteApp 集合中的应用](remoteapp-publish.md)。 （注意，这属于管理任务。）
2. 引导用户启动你发布的文件资源管理器应用，并使用它将文件复制并粘贴到用户的 UPD 内部和外部。

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>使用标准的网络文件复制将文件和数据上载到文件服务器
通常，组织使用文件服务器存储一般数据。 如果知道服务器名称或位置，用户可以浏览该服务器的本地网络，然后在那里复制他们的文件，这与上文所述的方法相似。 你将需要再次将文件资源管理器发布到 RemoteApp，然后将其与用户共享。

> [!NOTE]
> 文件服务器必须位于已部署 RemoteApp 的可路由网络上。
> 
> 

## <a name="copy-files-to-onedrive-for-business"></a>将文件复制到 OneDrive for Business
虽然无法在 RemoteApp 中启用 OneDrive for Business 同步代理，但仍可通过浏览器将文件从 UPD 复制到 OneDrive for Business。 

1. 将文件资源管理器发布到 RemoteApp，然后告知用户通过此应用访问文件。 
2. 将文件压缩后最易传输，因此用户应创建 .zip 文件，其中包含所有要迁移至 OneDrive for Business 的文件。
3. 要求用户转到 Office 365 门户，然后转到 OneDrive 并上载 .zip 文件。

## <a name="copy-files-by-using-drive-redirection"></a>使用驱动器重定向复制文件
如果已启用 [驱动器重定向](remoteapp-redirection.md)，则已创建用户的映射驱动器。 在此示例中，用户可以在重定向的驱动器上压缩他们的文件，然后将文件保存到用户的本地 PC。



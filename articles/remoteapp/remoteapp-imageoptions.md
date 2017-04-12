---
title: "创建 Azure RemoteApp 映像 | Microsoft Docs"
description: "了解可用于创建 Azure RemoteApp 映像的选项"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: cb0f9424-6185-45a1-abe9-c23f1edf34f2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: f2decec5385ffab59a441cdc28da80371b579df7
ms.lasthandoff: 03/31/2017


---
# <a name="create-an-azure-remoteapp-image"></a>创建 Azure RemoteApp 映像
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

Azure RemoteApp 使用映像来保存你与你的用户共享的应用。 （我们获取你的映像并使用它来创建 VM - 这正是用户登录到 Azure RemoteApp 后访问的内容。）若要使用你选择的应用程序创建 Azure RemoteApp 集合，无论是云还是混合，首先要使用这些安装的应用程序创建映像。 然后，创建使用该映像的集合，将用户分配到该集合，并将应用发布到这些用户。

有几个选项用于创建或使用映像。 映像的基本[要求](remoteapp-imagereqs.md)是运行 Windows Server 2012 R2，并且已经安装了远程桌面会话主机 (RDSH) 角色。 获取方式越来越有趣。

涉及到映像时，你有以下几个选项：

* 可以导入和使用[基于 Azure 虚拟机的映像](remoteapp-image-on-azurevm.md)。 这适合于需要自定义设置的业务线应用。 可以自定义映像以适合应用。
* 可以[创建和上载自定义映像](remoteapp-create-custom-image.md)。 如果你已拥有用于本地远程桌面服务部署的映像，则适合使用这种方式。
* 你可以使用 RemoteApp 订阅中包括的[模板映像](remoteapp-images.md)之一。 这些映像是由 RemoteApp 团队创建和维护的，而且包含一些你可以提供给你的用户的标准应用程序（如 Office 套件）。 请注意，只有 Office 365 Pro Plus 映像可以在生产设置中使用。

无论你从何处获取映像或者如何创建映像，都要确保你已了解[应用要求](remoteapp-appreqs.md)，以确保你的应用在 RemoteApp 中正常运行。 然后，下一步是创建[云](remoteapp-create-cloud-deployment.md)或[混合](remoteapp-create-hybrid-deployment.md)集合。



---
title: "Azure RemoteApp 疑难解答 - 应用程序启动和连接故障 | Microsoft Docs"
description: "了解如何解决启动和连接到 Azure RemoteApp 中的应用程序时的问题。"
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: e5cf7171-d1c2-4053-a38b-5af7821305e1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a91bea6a7a015ea4a61fd15ba34bcb1a86445b40


---
# <a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>解决 Azure RemoteApp 问题 - 应用程序启动和连接故障
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

在 Azure RemoteApp 中托管的应用程序可能由于几种不同的原因而无法启动。 本文介绍了各种原因以及用户尝试启动应用程序时可能会收到的错误消息， 还讨论了连接故障。 （但本文不介绍登录到 Azure RemoteApp 客户端时出现的问题。）  

继续阅读以获取有关由于应用启动和连接故障导致的常见错误消息的信息。

## <a name="were-getting-you-set-up-try-again-in-10-minutes"></a>我们正在准备...请在 10 分钟后重试。
此错误表示 Azure RemoteApp 正在扩大以满足你的用户的容量需求。 在后台将创建更多的 Azure RemoteApp VM 实例以处理你的用户的容量需求。 通常，这需要大约 5 分钟，但可能多达 10 分钟。 有时，此操作进行地并不快，并且立即需要资源。 例如，9 AM 方案中很多用户需要在 Azure RemoteAppn 中同时使用你的应用。 如果你遇到这种情况，我们可以在后端启用**容量模式**。 若要这样做，请打开 Azure 支持票证和/或发送电子邮件给我们（地址为 [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com)）。 请务必在请求中包括你的订阅 ID。  

![我们正在准备](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>无法自动重新连接到你的应用程序，请重新启动应用程序
如果你正在使用 Azure RemoteApp，然后使你的电脑进入睡眠状态超过 4 小时之后唤醒你的电脑，并且 Azure RemoteApp 客户端尝试自动重新连接但超时，通常会出现此错误消息。  指示用户导航回到该应用程序并尝试从 Azure RemoteApp 客户端中将其打开。

![无法自动重新连接到你的应用程序](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>临时配置文件问题
当用户配置文件（用户配置文件磁盘）无法装载且用户收到了临时配置文件时，将发生此错误。  管理员应导航到 Azure 门户中的集合，然后转到“**会话**”选项卡，并尝试“**注销**”用户。 这将强制完全注销用户会话，然后让用户再次尝试启动应用。 如果操作失败，请联系 Azure 支持人员或发送电子邮件给我们（地址为 [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com)）。

## <a name="azure-remoteapp-has-stopped-working"></a>Azure RemoteApp 已停止工作
此错误消息表示 Azure RemoteApp 客户端遇到了问题，而且需要重新启动。 指示用户关闭：选择“**关闭程序**”，然后重新启动 Azure RemoteApp 客户端。  如果问题仍然存在，请打开 Azure 支持票证和/或发送电子邮件给我们（地址为 [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com)）。

![Azure RemoteApp 已停止工作](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>远程桌面连接访问此资源时出错。 重试连接或与系统管理员联系
这是一般的错误消息 - 请联系 Azure 支持人员和/或发送电子邮件给我们（地址为：[remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com)），以便我们可以进行调查。 

![一般的 Azure RemoteApp 消息](./media/remoteapp-apptrouble/ra-apptrouble4.png) 




<!--HONumber=Nov16_HO3-->



---
title: "登录到经典 Azure VM | Microsoft Docs"
description: "使用 Azure 经典门户登录到使用经典部署模型创建的 Windows 虚拟机。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: b2a66b134d42e0b2d965c1f3ae83f93bed9cdfba


---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>使用 Azure 经典门户登录到 Windows 虚拟机
在 Azure 经典门户中，使用“连接”按钮启动远程桌面会话，然后登录到 Windows VM。

是否要连接到 Linux VM？ 请参阅[如何登录到运行 Linux 的虚拟机](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

了解如何[使用新版 Azure 门户执行这些步骤](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

有关如何使用 Resource Manager 模型登录到 VM 的信息，请参阅[此处](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="video-walkthrough"></a>视频演练
下面是本教程中的步骤的视频演练。 它还介绍了用于连接到 Azure 中的 Windows VM 的终结点和公用/专用端口。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Logging-On-To-VM-Running-Windows-Server-on-Azure/player]



## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机
1. 登录到 Azure 经典门户。
2. 单击“虚拟机”，然后选择虚拟机。
3. 在页面底部的命令栏中，单击“连接”。
   
    ![登录到虚拟机](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)

> [!TIP]
> 如果“连接”按钮不可用，请参阅本文末尾的故障排除提示。
> 
> 

## <a name="log-on-to-the-virtual-machine"></a>登录到虚拟机
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>后续步骤
* 如果“连接”按钮处于非活动状态或者在使用远程桌面连接时遇到其他问题，请尝试重置配置。 在虚拟机仪表板的“速览”下，单击“重置远程配置”。
* 如果密码出现问题，可尝试重置密码。 在虚拟机仪表板的“速览”下，单击“重置密码”。

如果这些提示不起作用或不是所需的内容，请参阅[对与基于 Windows 的 Azure 虚拟机的远程桌面连接进行故障排除](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 此文将指导你完成诊断和解决常见问题。




<!--HONumber=Nov16_HO3-->



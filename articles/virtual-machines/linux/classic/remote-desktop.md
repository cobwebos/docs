---
title: "通过远程桌面连接到 Linux VM | Microsoft Docs"
description: "了解如何安装和配置远程桌面以连接到 Microsoft Azure Linux VM"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: b909d0d18452127cd33bbaa362a12ec414a22329
ms.lasthandoff: 03/27/2017


---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>使用远程桌面连接到 Microsoft Azure Linux VM
> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

## <a name="overview"></a>概述
RDP（远程桌面协议）是用于 Windows 的专用协议。 我们如何使用 RDP 远程连接至 Linux VM（虚拟机）？

此指南将为你提供答案！ 它将帮助你在 Microsoft Azure Linux VM 上安装和配置 xrdp，并且你能够从一台 Windows 计算机通过远程桌面连接与其连接。 在本指南中，我们将使用运行 Ubuntu 或 OpenSUSE 的 Linux VM 作为示例。

Xrdp 是一个开源 RDP 服务器，支持你从 Windows 计算机通过远程桌面连接连接到 Linux 服务器。 它比 VNC（虚拟网络计算）表现得更好。 VNC 具有“JPEG”质量和行为慢的特征，而 RDP 则快速清晰。

> [!NOTE]
> 你必须已有运行 Linux 的 Microsoft Azure VM。 若要创建和设置 Linux VM，请参阅 [Azure Linux VM 教程](createportal.md)。
> 
> 

## <a name="create-endpoint-for-remote-desktop"></a>为远程桌面创建终结点
在本文档中，我们将使用默认终结点 3389 进行远程连接。 因此，将 Linux VM 的 3389 终结点设置为远程桌面，如下所示：

![图像](./media/remote-desktop/no1.png)

如果不知道如何设置 VM 的终结点，请参阅[指南](setup-endpoints.md)。

## <a name="install-gnome-desktop"></a>安装 Gnome 桌面
通过 putty 连接至你的 Linux VM，并安装 `Gnome Desktop`。

对于 Ubuntu，使用：

    #sudo apt-get update
    #sudo apt-get install ubuntu-desktop


对于 OpenSUSE，使用：

    #sudo zypper install gnome-session

## <a name="install-xrdp"></a>安装 xrdp
对于 Ubuntu，使用：

    #sudo apt-get install xrdp

对于 OpenSUSE，使用：

> [!NOTE]
> 在下面的命令中，使用你正在使用的版本更新 OpenSUSE 版本，下面是 `OpenSUSE 13.2` 的一个示例命令。
> 
> 

    #sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>启动时启动 xrdp 并设置 xdrp 服务
对于 OpenSUSE，使用：

    #sudo systemctl start xrdp
    #sudo systemctl enable xrdp

对于 Ubuntu，安装后，在启动时会自动启动并启用 xrdp。

## <a name="using-xfce-if-you-are-using-ubuntu-version-later-than-ubuntu-1204lts"></a>如果你使用的是比 Ubuntu 12.04LTS 更高的 Ubuntu 版本，请使用 xfce
因为当前 xrdp 可能不支持比 Ubuntu 12.04LTS 更高的 Ubuntu 版本的 Gnome 桌面，我们将使用 `xfce` 桌面。

安装 `xfce`，使用：

    #sudo apt-get install xubuntu-desktop

然后启用 `xfce`，使用：

    #echo xfce4-session >~/.xsession

编辑配置文件 `/etc/xrdp/startwm.sh`，使用：

    #sudo vi /etc/xrdp/startwm.sh   

在 `/etc/X11/Xsession` 行之前添加 `xfce4-session` 行。

重新启动 xrdp 服务，使用：

    #sudo service xrdp restart


## <a name="connect-your-linux-vm-from-a-windows-machine"></a>从 Windows 计算机连接 Linux VM
在 Windows 计算机中，启动远程桌面客户端，输入 Linux VM DNS 名称，或转到 Azure 经典门户的 VM 的 `Dashboard`，单击 `Connect` 连接 Linux VM，此时将看到如下所示的登录窗口：

![图像](./media/remote-desktop/no2.png)

使用 Linux VM 的 `user` & `password` 登录，立即从 Microsoft Azure Linux VM 使用远程桌面！

## <a name="next"></a>下一步
有关使用 xrdp 的详细信息，请参阅[此文](http://www.xrdp.org/)。



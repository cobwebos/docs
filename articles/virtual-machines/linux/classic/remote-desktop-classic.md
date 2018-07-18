---
title: 通过远程桌面连接到 Linux VM | Microsoft Docs
description: 了解如何为经典部署模型安装和配置远程桌面以连接到 Microsoft Azure Linux VM
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 5e68774c3edb7d82fef388c593a6b96c52857be6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927734"
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>使用远程桌面连接到 Microsoft Azure Linux VM
> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[资源管理器和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 有关本文中的 Resource Manager 的更新版本，请参阅[此处](../use-remote-desktop.md)。

## <a name="overview"></a>概述
RDP（远程桌面协议）是用于 Windows 的专用协议。 我们如何使用 RDP 远程连接至 Linux VM（虚拟机）？

此指南将提供答案！ 它将帮助你在 Microsoft Azure Linux VM 上安装和配置 xrdp，帮助你从一台 Windows 计算机通过远程桌面连接与其连接。 在本指南中，我们将使用运行 Ubuntu 或 OpenSUSE 的 Linux VM 作为示例。

Xrdp 工具是一个开源 RDP 服务器，支持你从 Windows 计算机通过远程桌面连接连接到 Linux 服务器。 RDP 比 VNC（虚拟网络计算）的性能更好。 VNC 通过 JPEG 质量的图形呈现，可能会很慢，而 RDP 快速又清晰。

> [!NOTE]
> 必须已有运行 Linux 的 Microsoft Azure VM。 若要创建和设置 Linux VM，请参阅 [Azure Linux VM 教程](createportal-classic.md)。
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>为远程桌面创建终结点
在本文档中，我们将使用默认终结点 3389 进行远程连接。将 3389 终结点设置为 Linux VM 的`Remote Desktop`，如下所示：

![图像](./media/remote-desktop/endpoint-for-linux-server.png)

如果不知道如何设置 VM 终结点，请参阅[本指南](setup-endpoints.md)。

## <a name="install-gnome-desktop"></a>安装 Gnome 桌面
通过 `putty` 连接至 Linux VM，并安装 `Gnome Desktop`。

对于 Ubuntu，使用：

```bash
sudo apt-get update
sudo apt-get install ubuntu-desktop
```

对于 OpenSUSE，使用：

```bash
sudo zypper install gnome-session
```

## <a name="install-xrdp"></a>安装 xrdp
对于 Ubuntu，使用：

```bash
sudo apt-get install xrdp
```

对于 OpenSUSE，使用：

> [!NOTE]
> 使用以下命令中使用的版本更新 OpenSUSE 版本。 以下示例为 `OpenSUSE 13.2`。
> 
> 

```bash
sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc
```

## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>启动时启动 xrdp 并设置 xdrp 服务
对于 OpenSUSE，使用：

```bash
sudo systemctl start xrdp
sudo systemctl enable xrdp
```

对于 Ubuntu，安装后，在启动时会自动启动并启用 xrdp。

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>如果使用的是比 Ubuntu 12.04LTS 更高的 Ubuntu 版本，请使用 xfce
因为 xrdp 的当前版本对高于 Ubuntu 12.04LTS 的 Ubuntu 版本不支持 Gnome 桌面，我们将改而使用 `xfce` 桌面。

若要安装 `xfce`，请使用此命令：

```bash
sudo apt-get install xubuntu-desktop
```

然后使用此命令启用 `xfce`：

```bash
echo xfce4-session >~/.xsession
```

编辑配置文件 `/etc/xrdp/startwm.sh`：

```bash
sudo vi /etc/xrdp/startwm.sh   
```

在 `/etc/X11/Xsession` 行之前添加 `xfce4-session` 行。

若要重新启动 xrdp 服务，请使用：

```bash
sudo service xrdp restart
```

## <a name="connect-your-linux-vm-from-a-windows-machine"></a>从 Windows 计算机连接 Linux VM
在 Windows 计算机中，启动远程桌面客户端并输入 Linux VM DNS 名称。 或转到 Azure 门户中的 VM 仪表板并单击 `Connect` 连接到 Linux VM。 在这种情况下，将看到登录窗口：

![图像](./media/remote-desktop/no2.png)

使用 Linux VM 的用户名和密码登录。

## <a name="next-steps"></a>后续步骤
有关使用 xrdp 的详细信息，请参阅 [http://www.xrdp.org/](http://www.xrdp.org/)。

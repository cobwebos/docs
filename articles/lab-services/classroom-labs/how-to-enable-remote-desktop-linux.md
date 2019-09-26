---
title: 在 Azure 实验室服务中启用适用于 Linux 的远程桌面 |Microsoft Docs
description: 了解如何在 Azure 实验室服务中的实验室内启用适用于 Linux 虚拟机的远程桌面。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 86ad892f514513840ff33e46921607221896db84
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315735"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>在 Azure 实验室服务的实验室中启用适用于 Linux 虚拟机的远程桌面
本文介绍如何执行以下任务：

- 启用适用于 Linux VM 的远程桌面
- 教师如何通过远程桌面连接（RDP）连接到模板 VM。

## <a name="enable-remote-desktop-for-linux-vm"></a>启用适用于 Linux VM 的远程桌面
在创建实验室的过程中，教师可以为**Linux**映像启用**远程桌面连接**。 为模板选择 Linux 映像时，将显示 "**启用远程桌面连接**" 选项。 启用此选项后，教师可以通过 RDP （远程桌面）连接到模板 VM 和学生 Vm。 

![为 Linux 映像启用远程桌面连接](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

在 "**启用远程桌面连接**消息" 框中，选择 "继续" "**远程桌面**"。 

![为 Linux 映像启用远程桌面连接](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> 启用**远程桌面连接**仅打开 Linux 计算机上的**RDP**端口。 如果已在虚拟机映像上安装并配置了 RDP （例如：Ubuntu Data Science Virtual Machine 映像），你/学生可以通过 RDP 连接到 Vm，而无需执行任何其他步骤。
> 
> 如果 VM 映像未安装和配置 RDP，则需要首次使用 SSH 连接到 Linux 计算机，并安装 RDP 和 GUI 包，以便你/学生以后可以使用 RDP 连接到 Linux 计算机。 然后，发布该映像，以便学生可以通过 RDP 登录到学生 Linux Vm。

## <a name="supported-operating-systems"></a>支持的操作系统
目前，以下操作系统支持远程桌面连接：

- openSUSE Leap 42.3
- 基于 CentOS 的 7.5
- Debian 9“Stretch”
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>使用 RDP 连接到模板 VM 的教师
教师必须首先使用 SSH 连接到模板 VM，并在其上安装 RDP 和 GUI 包。 然后，教师可以通过以下步骤使用 RDP 连接到 Linux Vm： 

创建实验室时，可以看到 "**远程桌面**" 选项，用于连接到模板 VM。 

![创建时通过 RDP 连接到模板](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

创建实验室并启动模板 VM 后，会在实验室主页上看到 "**远程桌面**" 选项。 如果模板 VM 尚未启动，则启动它。 

![创建实验室后，通过 RDP 连接到模板](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

有关使用 SSH 或 RDP 连接到 VM 的详细信息，请参阅 [使用 SSH 或 RDP 进行连接] （（#connect-SSH 或 rdp）。 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>使用 RDP 连接到学生 VM 的教师
教师/教授可以通过切换到 "**虚拟机**" 视图并选择 "**连接**" 图标来连接到学生 VM。 在此之前，教师必须**发布**模板映像，并在其上安装 RDP 和 GUI 包。 

![教师正在连接到学生 VM](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

有关使用 SSH 或 RDP 连接到 VM 的详细信息，请参阅 [使用 SSH 或 RDP 进行连接] （（#connect-SSH 或 rdp）。 

## <a name="connect-using-ssh-or-rdp"></a>使用 SSH 或 RDP 进行连接
如果选择**SSH**选项，将看到以下 "**连接到虚拟机**" 对话框：  

![SSH 连接字符串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

选择文本框旁边的 "**复制**" 按钮，将其复制到剪贴板。 保存 SSH 连接字符串。 从 SSH 终端使用此连接字符串（如 [Putty](https://www.putty.org/)）连接到虚拟机。

如果选择**rdp**选项，则会将 rdp 文件下载到计算机上。 保存并打开它以连接到计算机。 

## <a name="next-steps"></a>后续步骤
当指导员启用了远程桌面连接功能后，学生可通过 RDP/SSH 连接到其 Vm。 有关详细信息，请参阅[在课堂实验室中使用适用于 Linux vm 的远程桌面](how-to-use-remote-desktop-linux-student.md)。 
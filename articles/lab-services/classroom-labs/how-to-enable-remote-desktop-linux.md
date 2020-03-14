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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270857"
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
> 启用**远程桌面连接**仅打开 Linux 计算机上的**RDP**端口。 如果已在虚拟机映像上安装并配置了 RDP （例如： Ubuntu Data Science Virtual Machine 映像），则你/学生可以通过 RDP 连接到 Vm，而无需执行任何其他步骤。
> 
> 如果 VM 映像未安装和配置 RDP，则需要首次使用 SSH 连接到 Linux 计算机，并安装 RDP 和 GUI 包，以便你/学生以后可以使用 RDP 连接到 Linux 计算机。 有关详细信息，请参阅[安装和配置远程桌面以连接到 Azure 中的 LINUX VM](../../virtual-machines/linux/use-remote-desktop.md)。 然后，发布该映像，以便学生可以通过 RDP 登录到学生 Linux Vm。 

## <a name="supported-operating-systems"></a>支持的操作系统
目前，以下操作系统支持远程桌面连接：

- openSUSE Leap 42.3
- 基于 CentOS 的7。5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>连接到模板 VM 
教师必须首先使用 SSH 连接到模板 VM，并在其上安装 RDP 和 GUI 包。 然后，教师可以使用 RDP 连接到模板 VM： 

1. 如果在工具栏上看到 "**自定义模板**"，请选择它。 然后，在 "**自定义模板**" 对话框中选择 "**继续**"。 此操作将启动模板 VM。  

    ![自定义模板](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. 模板 VM 启动后，可以选择 "**连接模板**"，然后在工具栏上**通过 SSH 进行连接**。 

    ![创建实验室后，通过 RDP 连接到模板](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. 将看到以下 "**连接到虚拟机**" 对话框。 选择文本框旁边的 "**复制**" 按钮，将其复制到剪贴板。 保存 SSH 连接字符串。 从 SSH 终端使用此连接字符串（如 [Putty](https://www.putty.org/)）连接到虚拟机。
 
    ![SSH 连接字符串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. 安装 RDP 和 GUI 包，以便你/学生可以在以后使用 RDP 连接到 Linux 计算机。 有关详细信息，请参阅[安装和配置远程桌面以连接到 Azure 中的 LINUX VM](../../virtual-machines/linux/use-remote-desktop.md)。 然后，发布该映像，以便学生可以通过 RDP 登录到学生 Linux Vm。
5. 安装这些包后，可以使用工具栏上的 "**连接到" 模板**，并选择 "**通过 rdp 连接**"，通过 RDP 连接到模板 VM。 保存 RDP 文件，并使用它通过 RDP 连接到模板 VM。 

## <a name="next-steps"></a>后续步骤
当指导员启用了远程桌面连接功能后，学生可通过 RDP/SSH 连接到其 Vm。 有关详细信息，请参阅[在课堂实验室中使用适用于 Linux vm 的远程桌面](how-to-use-remote-desktop-linux-student.md)。 
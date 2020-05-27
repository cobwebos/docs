---
title: 在 Azure 实验室服务中启用适用于 Linux 的远程桌面 | Microsoft Docs
description: 了解如何在 Azure 实验室服务的实验室中启用适用于 Linux 虚拟机的远程桌面。
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
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a40575340fc5b1c202be6b001807085954439f03
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588150"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>在 Azure 实验室服务的实验室中启用适用于 Linux 虚拟机的远程桌面
本文演示如何完成以下任务：

- 为 Linux VM 启用远程桌面
- 教师如何通过远程桌面连接 (RDP) 连接到模板 VM。

## <a name="enable-remote-desktop-for-linux-vm"></a>为 Linux VM 启用远程桌面
在创建实验室期间，教师可以为 **Linux** 映像启用**远程桌面连接**。 为模板选择 Linux 映像时，会显示“启用远程桌面连接”选项。 启用此选项后，教师可以通过 RDP（远程桌面）连接到模板 VM 和学生 VM。 

![为 Linux 映像启用远程桌面连接](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

在“启用远程桌面连接”消息框中，选择“继续启用远程桌面”。 

![为 Linux 映像启用远程桌面连接](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> 启用远程桌面连接只会打开 Linux 计算机上的 RDP 端口。 如果已在虚拟机映像上安装并配置了 RDP，则你/学生可以通过 RDP 连接到 VM，无需执行任何其他步骤。
> 
> 如果 VM 映像未安装并配置 RDP，则你首次需要使用 SSH 连接到 Linux 计算机，并安装 RDP 和 GUI 包，以便你/学生以后可以使用 RDP 连接到 Linux 计算机。 有关详细信息，请参阅[安装并配置远程桌面以连接到 Azure 中的 Linux VM](../../virtual-machines/linux/use-remote-desktop.md)。 然后，你发布该映像，以便学生可以通过 RDP 登录到学生 Linux VM。 

## <a name="supported-operating-systems"></a>支持的操作系统
目前，以下操作系统支持远程桌面连接：

- openSUSE Leap 42.3
- 基于 CentOS 的 7.5
- Debian 9 Stretch
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>连接到模板 VM 
教师必须首先使用 SSH 连接到模板 VM，并在其上安装 RDP 和 GUI 程序包。 然后，教师可以使用 RDP 连接到模板 VM： 

1. 如果在工具栏上看到“自定义模板”，请选择它。 然后，在“自定义模板”对话框中选择“继续”。 此操作会启动模板 VM。  

    ![自定义模板](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. 在模板 VM 启动后，你可以在工具栏上选择“连接到模板”，然后选择“通过 SSH 进行连接”。 

    ![创建实验室后，通过 RDP 连接到模板](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. 你会看到下面的“连接到虚拟机”对话框。 选择文本框旁的“复制”按钮，将其复制到剪贴板。 保存 SSH 连接字符串。 从 SSH 终端使用此连接字符串（如 [Putty](https://www.putty.org/)）连接到虚拟机。
 
    ![SSH 连接字符串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. 安装 RDP 和 GUI 包，以便你/学生以后可以使用 RDP 连接到 Linux 计算机。 有关详细信息，请参阅[安装并配置远程桌面以连接到 Azure 中的 Linux VM](../../virtual-machines/linux/use-remote-desktop.md)。 然后，你发布该映像，以便学生可以通过 RDP 登录到学生 Linux VM。
5. 安装这些包后，可以使用工具栏上的“连接到模板”，然后选择“通过 RDP 进行连接”以通过 RDP 连接到模板 VM。 保存 RDP 文件，然后使用它通过 RDP 连接到模板 VM。 

## <a name="next-steps"></a>后续步骤
在讲师启用远程桌面连接功能后，学生可以通过 RDP/SSH 连接到其 VM。 有关详细信息，请参阅[为课堂实验室中的 Linux VM 使用远程桌面](how-to-use-remote-desktop-linux-student.md)。 
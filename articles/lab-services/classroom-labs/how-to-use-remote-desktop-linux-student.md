---
title: 在 Azure 实验室服务中使用适用于 Linux 的远程桌面 | Microsoft Docs
description: 了解如何在 Azure 实验室服务的实验室中使用适用于 Linux 虚拟机的远程桌面。
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
ms.openlocfilehash: 8ccad0698ea6560dd183cacc71f5f3a644e8220c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588099"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>在 Azure 实验室服务的课堂实验室中使用适用于 Linux 虚拟机的远程桌面
本文说明了学生如何使用 RDP/SSH 连接到实验室中的 Linux 虚拟机 (VM)。 

讲师需要先启用远程桌面连接功能，然后学生才能连接到课堂实验室的 VM。 有关讲师如何启用远程桌面连接功能的说明，请参阅[为 Linux 虚拟机启用远程桌面](how-to-enable-remote-desktop-linux.md)。

> [!IMPORTANT] 
> 启用远程桌面连接只会打开 Linux 计算机上的 RDP 端口。 讲师首次可以使用 SSH 连接到 Linux 计算机，并安装 RDP 和 GUI 包，以便你可以在以后使用 RDP 连接到 Linux 计算机。 

## <a name="connect-to-the-student-vm"></a>连接到学生 VM
在实验室所有者（教师）发布其上安装了 RDP 和 GUI 包的模板 VM 之后，学生可以使用 RDP 登录到其 Linux VM。 以下是具体步骤： 

1. 当学生直接 (`https://labs.azure.com`) 或使用注册链接 (`https://labs.azure.com/register/<registrationCode>`) 登录实验室门户后，会显示该学生有权访问的每个实验室的磁贴。 
2. 在磁贴上，如果 VM 处于停止状态，则切换按钮以启动 VM。 
3. 选择“连接”。 你会看到两个用于连接到 VM 的选项：“SSH”和“远程桌面”。

    ![学生 VM - 连接选项](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>使用 SSH 或 RDP 进行连接
如果选择“SSH”选项，则会看到以下“连接到虚拟机”对话框：  

![SSH 连接字符串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

选择文本框旁的“复制”按钮，将其复制到剪贴板。 保存 SSH 连接字符串。 从 SSH 终端使用此连接字符串（如 [Putty](https://www.putty.org/)）连接到虚拟机。

如果选择“RDP”选项，则会将 RDP 文件下载到计算机上。 保存并打开该文件以连接到计算机。 

## <a name="next-steps"></a>后续步骤
若要了解如何在课堂实验室中为 Linux VM 启用远程桌面连接功能，请参阅[为 Linux 虚拟机启用远程桌面](how-to-enable-remote-desktop-linux.md)。 


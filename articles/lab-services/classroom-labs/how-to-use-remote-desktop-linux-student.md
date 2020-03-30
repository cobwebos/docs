---
title: 在 Azure 实验室服务中使用远程桌面进行 Linux |微软文档
description: 了解如何在 Azure 实验室服务中的实验室中使用 Linux 虚拟机的远程桌面。
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
ms.openlocfilehash: 0f45af2730b05998fc82212c63778c89bb16b6ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73585071"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>在 Azure 实验室服务的教室实验室中为 Linux 虚拟机使用远程桌面
本文介绍学生如何使用 RDP/SSH 连接到实验室中的 Linux 虚拟机 （VM）。 

教师需要启用远程桌面连接功能，然后学生才能连接到教室实验室的 VM。 有关讲师如何启用远程桌面连接功能的说明，请参阅[为 Linux 虚拟机启用远程桌面](how-to-enable-remote-desktop-linux.md)。

> [!IMPORTANT] 
> 启用**远程桌面连接**仅在 Linux 计算机上打开**RDP**端口。 教师首次可以使用 SSH 连接到 Linux 计算机，并安装 RDP 和 GUI 包，以便以后可以使用 RDP 连接到 Linux 计算机。 

## <a name="connect-to-the-student-vm"></a>连接到学生 VM
在实验室所有者（教师/教授）**发布**模板 VM 并在计算机上安装了 RDP 和 GUI 包后，学生可以将 RDP 连接到其 Linux VM。 以下是具体步骤： 

1. 当学生直接登录到实验室门户 （）`https://labs.azure.com`或使用注册链接 （`https://labs.azure.com/register/<registrationCode>`）， 将显示学生有权访问的每个实验室的磁贴。 
2. 在磁贴上，如果按钮处于停止状态，则切换按钮以启动 VM。 
3. 选择“连接”****。 您将看到两个连接到 VM 的选项 **：SSH**和**远程桌面**。

    ![学生 VM - 连接选项](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>使用 SSH 或 RDP 连接
如果选择**SSH**选项，您将看到以下 **"连接到虚拟机**"对话框：  

![SSH 连接字符串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

选择文本框旁边的 **"复制**"按钮以将其复制到剪贴板。 保存 SSH 连接字符串。 从 SSH 终端使用此连接字符串（如 [Putty](https://www.putty.org/)）连接到虚拟机。

如果选择**RDP**选项，RDP 文件将下载到您的计算机上。 保存并打开它以连接到计算机。 

## <a name="next-steps"></a>后续步骤
要了解如何在教室实验室中为 Linux VM 启用远程桌面连接功能，请参阅[为 Linux 虚拟机启用远程桌面](how-to-enable-remote-desktop-linux.md)。 


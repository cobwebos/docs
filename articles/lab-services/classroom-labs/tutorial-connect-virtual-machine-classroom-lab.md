---
title: 访问 Azure 实验室服务中的教室实验室 | Microsoft Docs
description: 在本教程中，会访问课堂实验室中由教授设置的虚拟机。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 5482ea720ea8d21230587dd9216bd006bf4e5a6e
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650642"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>教程：访问 Azure 实验室服务中的课堂实验室
在本教程中，你会作为一名学生连接到教室实验室中的虚拟机 (VM)。 

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 使用注册链接 
> * 连接到虚拟机

## <a name="use-the-registration-link"></a>使用注册链接

1. 导航到从教授/教师处收到的注册 URL。 完成注册后，不需使用注册 URL。 请改用 URL：[https://labs.azure.com](https://labs.azure.com)。 
1. 使用学校帐户登录服务以完成注册。 
2. 注册后，请确认可看到你有权访问的实验室的虚拟机。 
3. 等待虚拟机准备就绪，然后**启动** VM。 此过程需要一些时间。  

    ![启动 VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

1. 在要访问的实验室虚拟机的磁贴上，选择“连接”。 

    ![连接到 VM](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. 执行以下步骤之一： 
    1. 对于 Windows 虚拟机，将 RDP 文件保存到硬盘上。 打开要连接到虚拟机的 RDP 文件。 使用从教师/教授处获得的用户名和密码登录到计算机。 
    3. 对于 **Linux** 虚拟机，可以使用 **SSH** 或 **RDP**（如果已启用）连接到它们。 有关详细信息，请参阅[为 Linux 计算机启用远程桌面连接](how-to-enable-remote-desktop-linux.md)。 

## <a name="next-steps"></a>后续步骤
本教程使用了从教师/教授处获取的注册链接来访问教室实验室。

作为实验室所有者，你希望查看已注册到实验室的用户并跟踪 VM 的使用情况。 转到下一教程，了解如何跟踪实验室的使用情况：

> [!div class="nextstepaction"]
> [跟踪实验室的使用情况](tutorial-track-usage.md) 

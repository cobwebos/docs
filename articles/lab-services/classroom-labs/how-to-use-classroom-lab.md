---
title: 如何访问 Azure 实验室服务中的课堂实验室 | Microsoft Docs
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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 8e20f612bc54433091036377d51a7e59e3abec51
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402144"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>如何访问 Azure 实验室服务中的课堂实验室
本文介绍如何访问课堂实验室、连接到实验室中的 VM 以及停止 VM。 

## <a name="register-to-a-lab"></a>注册到实验室
1. 导航到从教授/教师处收到的注册 URL。 
2. 使用学校帐户登录服务以完成注册。 
3. 注册后，请确认可看到你有权访问的实验室的虚拟机。 
2. 等待虚拟机准备就绪，然后**启动** VM。 此过程需要一些时间。  

    ![启动 VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>查看所有课堂实验室
在注册到实验室以后，可以通过下述步骤查看所有的教室实验室： 

1. 导航到 [https://labs.azure.com](https://labs.azure.com)。 
2. 使用注册到实验室时使用过的用户帐户登录到服务。 
3. 确认看到可以访问的所有实验室。 

    ![查看所有实验室](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>连接到课堂实验室中的虚拟机

1. 启动 VM（如果尚未启动），方法是在磁贴上选择“启动”。 
2. 在表示想要访问的实验室虚拟机的磁贴上，选择“连接”。 
3. 执行以下步骤中的一个： 
   1. 对于 Windows 虚拟机，将 RDP 文件保存到硬盘上。 打开要连接到虚拟机的 RDP 文件。 使用从教师/教授处获得的用户名和密码登录到计算机。 
   3. 对于 Linux 虚拟机，复制和保存“连接到虚拟机”对话框上的 SSH 连接字符串。 从 SSH 终端使用此连接字符串（如 [Putty](https://www.putty.org/)）连接到虚拟机。

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>停止课堂实验室中的虚拟机

若要停止 VM，请选择磁贴上的“停止”。 当 VM 停止后，启用磁贴上的“启动”按钮。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以管理员身份创建并管理实验室帐户](how-to-manage-lab-accounts.md)
- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
 
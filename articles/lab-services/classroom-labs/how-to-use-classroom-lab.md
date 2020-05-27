---
title: 如何访问 Azure 实验室服务中的课堂实验室 | Microsoft Docs
description: 在本教程中，你将访问教师在课堂实验室中设置的虚拟机。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 2430348a8bfbecda3f172361a40a96ef801f5bc4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592434"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>如何访问 Azure 实验室服务中的课堂实验室
本文介绍如何注册到课堂实验室、查看可以访问的所有实验室、在实验室中启动/停止 VM 以及连接到 VM。 

## <a name="register-to-the-lab"></a>注册到实验室

1. 转到从教师处收到的注册 URL。 完成注册后，不需使用注册 URL。 请改用 URL：[https://labs.azure.com](https://labs.azure.com)。 目前尚不支持 Internet Explorer 11。 

    ![注册到实验室](../media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. 使用学校帐户登录服务以完成注册。 

    > [!NOTE]
    > 使用 Azure 实验室服务需要 Microsoft 帐户。 如果尝试使用非 Microsoft 帐户（例如 Yahoo 或 Google 帐户）登录到门户，请按照说明创建将链接到非 Microsoft 帐户的 Microsoft 帐户。 然后，按照步骤完成注册过程。 
1. 注册后，请确认可看到你有权访问的实验室的虚拟机。 

    ![可访问 VM](../media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. 等到虚拟机准备就绪。 在“VM”磁贴上，请注意以下字段：
    1. 在磁贴的顶部，可以看到**实验室的名称**。
    1. 在其右侧，可以看到代表 VM 的**操作系统 (OS)** 的图标。 在此示例中，它是 Windows OS。 
    1. 可以在磁贴底部看到用于启动/停止 VM 以及连接到 VM 的图标/按钮。 
    1. 在按钮的右侧，可以看到 VM 的状态。 确认你看到 VM 的状态为“已停止”。

        ![VM 处于停止状态](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>启动或停止 VM
1. 通过选择第一个按钮**启动** VM，如下图所示。 此过程需要一些时间。  

    ![启动 VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. 确认 VM 的状态设置为“正在运行”。 

    ![VM 处于正在运行状态](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    请注意，第一个按钮的图标已更改为表示**停止**操作。 可以选择此按钮以停止 VM。 

## <a name="connect-to-the-vm"></a>连接到 VM

1. 选择下图中所示的第二个按钮以**连接**到实验室的 VM。 

    ![连接到 VM](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. 执行以下步骤中的一个： 
    1. 对于 Windows 虚拟机，将 RDP 文件保存到硬盘上 。 打开要连接到虚拟机的 RDP 文件。 使用从教师处获得的用户名和密码来登录计算机。 
    3. 对于 **Linux** 虚拟机，可以使用 **SSH** 或 **RDP**（如果已启用）连接到它们。 有关详细信息，请参阅[为 Linux 计算机启用远程桌面连接](how-to-enable-remote-desktop-linux.md)。 
    1. 如果使用 **Mac** 连接到实验室 VM，请按照下一部分中的说明进行操作。 

## <a name="progress-bar"></a>进度栏 
磁贴上的进度栏会显示对分配给你的[配额小时](how-to-configure-student-usage.md#set-quotas-for-users)数已使用的小时数。 此时间是在实验室的计划时间之外，额外分配给你的时间。 进度栏和进度栏下文本的颜色会根据以下情况而变化：

- 如果课程正在进行（在课程计划内），则进度栏呈灰色显示，表示未使用配额小时数。 

    ![灰色进度栏](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- 如果未分配配额（零小时），则会显示**仅在课程期间可用**的文本以代替进度栏。 
    
    ![未设置配额时的状态](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- 如果**用尽了配额**，则进度栏的颜色为**红色**。 

    ![红色进度栏](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- 当进度栏超出了实验室的计划时间并且已使用了部分配额时间时，进度栏的颜色为**蓝色**。 

    ![蓝色进度栏](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>查看所有课堂实验室
在注册到实验室以后，可以通过下述步骤查看所有的教室实验室： 

1. 导航到 [https://labs.azure.com](https://labs.azure.com)。 目前尚不支持 Internet Explorer 11。 
2. 使用注册到实验室时使用过的用户帐户登录到服务。 
3. 确认看到可以访问的所有实验室。 

    ![查看所有实验室](../media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以管理员身份创建并管理实验室帐户](how-to-manage-lab-accounts.md)
- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
 
---
title: 在 Azure 实验室服务中管理课堂实验室的模板 | Microsoft Docs
description: 了解如何在 Azure 实验室服务中创建和管理课堂实验室模板。
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: fcf31fcc266358911612c25e0b73a0a9de696b1d
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539004"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>在 Azure 实验室服务中创建和管理课堂模板
实验室中的模板是基础虚拟机映像，用户的所有虚拟机均依据其创建。 设置模板虚拟机，为其准确配置需要提供给实验室用户的内容。 你可以提供实验室用户看到的模板的名称和描述。 然后发布此模板，让模板 VM 的实例可供实验室用户使用。 发布模板时，Azure 实验室服务会使用该模板在实验室中创建 VM。 在此过程中创建的 VM 数等于允许进入实验室的最大用户数，这可以在实验室的使用策略中设置。 所有虚拟机的配置都与模板相同。

本文介绍如何在 Azure 实验室服务的课堂实验室中创建和管理模板虚拟机。 

## <a name="set-or-update-template-title-and-description"></a>设置或更新模板标题和说明
使用以下步骤来首次设置标题和说明，并在之后更新。 

1. 在 "**模板**" 页上，输入实验室的新**标题**。  
2. 输入模板的新**说明**。 将焦点移出文本框时，它会自动保存。 

    ![模板名称和说明](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>更新模板 VM
使用以下步骤更新模板 VM。  

1. 等待模板 VM 开始，然后选择工具栏上的 "**连接到模板**" 以连接到模板 vm，并按照说明进行操作。 如果是 Windows 计算机，你将看到一个用于下载 RDP 文件的选项。 
1. 一旦你连接到模板并进行了更改，它将不再与上次发布到用户的虚拟机具有相同的设置。 在再次发布后，模板更改将不会反映在用户的现有虚拟机上。

    ![连接到模板 VM](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. 安装学生进行实验室操作所需的任何软件（例如，Visual Studio、Azure 存储资源管理器，等等）。 
1. 断开与模板 VM 的连接（关闭远程桌面会话）。 
1. 通过选择 "**停止模板**"**停止**模板 VM。 
1. 按照下一节中的步骤来**发布**更新的模板 VM。 

## <a name="publish-the-template-vm"></a>发布模板 VM  
在此步骤中，将发布模板 VM。 发布模板 VM 时，Azure 实验室服务使用模板在实验室中创建 Vm。 所有虚拟机的配置都与模板相同。


1. 在 "**模板**" 页上，选择工具栏上的 "**发布**"。 

    ![“发布模板”按钮](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 发布后无法取消发布。 
2. 在“发布模板”页上，输入要在实验室中创建的虚拟机的数目，然后选择“发布”。 

    ![发布模板 - VM 数](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. 可以在页面上查看模板的**发布状态**。 此过程最长可能需要花费一小时。 

    ![发布模板 - 进度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 等待发布完成，然后在左侧菜单中选择 "**虚拟机**" 或选择 "**虚拟机**" 磁贴，切换到 "**虚拟机池**" 页。 确认看到这些虚拟机处于“未分配”状态。 这些 VM 尚未分配给学生。 它们应该处于“已停止”状态。 可以在此页上启动学生 VM，连接到该 VM，停止该 VM，然后删除该 VM。 你可以在此页中启动这些 VM，也可以让你的学生启动它们。 

    ![处于已停止状态的虚拟机](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以管理员身份创建并管理实验室帐户](how-to-manage-lab-accounts.md)
- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)

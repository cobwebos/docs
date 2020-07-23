---
title: 在 Azure 实验室服务中管理课堂实验室的模板 | Microsoft Docs
description: 了解如何在 Azure 实验室服务中创建和管理课堂实验室模板。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f7c74f75de3d24acd01330910bf6ee3f8f18533
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445791"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>在 Azure 实验室服务中创建和管理课堂模板
实验室中的模板是基础虚拟机映像，用户的所有虚拟机均依据其创建。 设置模板虚拟机，为其准确配置需要提供给实验室用户的内容。 你可以提供实验室用户看到的模板的名称和描述。 然后发布此模板，让模板 VM 的实例可供实验室用户使用。 发布模板时，Azure 实验室服务会使用该模板在实验室中创建 VM。 在此过程中创建的 VM 数等于允许进入实验室的最大用户数，这可以在实验室的使用策略中设置。 所有虚拟机的配置都与模板相同。

本文介绍如何在 Azure 实验室服务的课堂实验室中创建和管理模板虚拟机。 

> [!NOTE]
> 创建实验室时，模板 VM 将会创建，但不会启动。 可以启动它，连接到它，并为实验室安装任何必备软件，然后发布它。 发布模板 VM 时，如果未关闭它，则它会自动关闭。 
> 
> 模板 VM 在运行时会产生**成本**，因此，请确保在不需要运行模板 VM 时将它关闭。 


## <a name="set-or-update-template-title-and-description"></a>设置或更新模板标题和说明
使用以下步骤来首次设置标题和说明，并在之后更新。 

1. 在“模板”页上，输入实验室的新**标题**。  
2. 输入模板的新**说明**。 将焦点移出文本框时，其中的内容会自动保存。 

    ![模板名称和说明](./media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>更新模板 VM
使用以下步骤更新模板 VM。  

1. 等待模板 VM 启动，在工具栏上选择“连接到模板”以连接到模板 VM，然后按照说明操作。 如果在 Windows 计算机上操作，你将看到一个用于下载 RDP 文件的选项。 
1. 连接到模板并做出更改后，模板 VM 的设置不再与上次发布给用户的虚拟机的设置相同。 除非再次发布模板，否则模板更改将不会反映在用户的现有虚拟机上。

    ![连接到模板 VM](./media/how-to-create-manage-template/connect-template-vm.png)
    
1. 安装学生进行实验室操作所需的任何软件（例如，Visual Studio、Azure 存储资源管理器，等等）。 
1. 断开与模板 VM 的连接（关闭远程桌面会话）。 
1. 选择“停止模板”以**停止**模板 VM。 
1. 按照下一部分中的步骤**发布**已更新的模板 VM。 

## <a name="publish-the-template-vm"></a>发布模板 VM  
在此步骤中，我们发布模板 VM。 你发布模板 VM 时，Azure 实验室服务会使用该模板在实验室中创建 VM。 所有虚拟机的配置都与模板相同。


1. 在“模板”页上的工具栏中选择“发布”。 

    ![“发布模板”按钮](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 发布后无法取消发布。 
2. 在“发布模板”页上，输入要在实验室中创建的虚拟机的数目，然后选择“发布”。 

    ![发布模板 - VM 数](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. 可以在页面上查看模板的**发布状态**。 此过程最长可能需要花费一小时。 

    ![发布模板 - 进度](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 等待发布完成，然后选择左侧菜单上的“虚拟机”或选择“虚拟机”磁贴，切换到“虚拟机池”页。   确认看到这些虚拟机处于“未分配”状态。 这些 VM 尚未分配给学生。 它们应该处于“已停止”状态。 可以在此页上启动学生 VM，连接到该 VM，停止该 VM，然后删除该 VM。 你可以在此页中启动这些 VM，也可以让你的学生启动它们。 

    ![处于已停止状态的虚拟机](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以管理员身份创建并管理实验室帐户](how-to-manage-lab-accounts.md)
- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)

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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: f0fc1e143ce7d271d5faaa8dda0eb40cdfc9e006
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332744"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>在 Azure 实验室服务中创建和管理课堂模板
实验室中的模板是基础虚拟机映像，用户的所有虚拟机均依据其创建。 设置模板虚拟机，为其准确配置需要提供给实验室用户的内容。 你可以提供实验室用户看到的模板的名称和描述。 然后发布此模板，让模板 VM 的实例可供实验室用户使用。 发布模板时，Azure 实验室服务会使用该模板在实验室中创建 VM。 在此过程中创建的 VM 数等于允许进入实验室的最大用户数，这可以在实验室的使用策略中设置。 所有虚拟机的配置都与模板相同。

本文介绍如何在 Azure 实验室服务的课堂实验室中创建和管理模板虚拟机。 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>创建课堂实验室时发布模板
若要了解如何在创建教室实验室时发布模板，请参阅[创建教室实验室](how-to-manage-classroom-labs.md#create-a-classroom-lab)
 
## <a name="set-or-update-template-title-and-description"></a>设置或更新模板标题和说明
使用以下步骤来首次设置标题和说明，并在之后更新。 

1. 在 "**模板**" 页上，输入实验室的新**标题**。  
2. 输入模板的新**说明**。 将焦点移出文本框时，它会自动保存。 

    ![模板名称和说明](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>更新模板 VM
使用以下步骤更新模板 VM。  

1. 在 "**模板**" 页上，选择工具栏上的 "**自定义模板**"。 

    ![自定义模板按钮](../media/how-to-create-manage-template/customize-template-button.png)
2. 在 "**自定义模板**" 对话框中，选择 "**继续**"。 一旦你启动了模板并进行了更改，它将不再与上次发布到用户的虚拟机具有相同的设置。 在再次发布后，模板更改将不会反映在用户的现有虚拟机上。

    !["自定义" 对话框](../media/how-to-create-manage-template/customize-template-dialog.png)
1. 在工具栏上选择 "**连接到模板**" 按钮连接到模板 VM，并按照说明进行操作。 如果是 Windows 计算机，你将看到一个用于下载 RDP 文件的选项。 

    ![连接到模板 VM](../media/how-to-create-manage-template/connect-template-vm.png)
1. 安装学生进行实验室操作所需的任何软件（例如，Visual Studio、Azure 存储资源管理器，等等）。 
2. 断开与模板 VM 的连接（关闭远程桌面会话）。 
3. 通过选择 "**停止模板**"**停止**模板 VM。 
4. 按照下一节中的步骤来**发布**更新的模板 VM。 

## <a name="publish-the-template-vm"></a>发布模板 VM  
如果不在创建实验室时发布模板，可在以后再发布。 在发布之前，可能需要连接到模板 VM，并使用任何软件对其进行更新。 发布模板时，Azure 实验室服务会使用该模板在实验室中创建 VM。 在此过程中创建的 Vm 数为首次发布时指定的 Vm 数，或在 "虚拟机池" 页上指定的数量。 所有虚拟机的配置都与模板相同。 

1. 在 "**模板**" 页上，选择工具栏上的 "**发布**"。 
1. 在“发布模板”消息框中查看消息，然后选择“发布”。 此过程可能需要一些时间，具体取决于所要创建的 VM 数量。

    ![发布按钮](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > 模板一旦发布，便不能取消发布。 不过，可以重新发布模板。 
4. 您可以在 "模板" 页上查看发布过程的状态。 等待模板的状态更改为“已发布”。 

    ![发布状态](../media/how-to-create-manage-template/publish-status.png)
1. 切换到“虚拟机”页，确认看到这些虚拟机处于“未分配”状态。 这些 VM 尚未分配给学生。 等待 VM 创建完毕。 它们应该处于“已停止”状态。 可以在此页上启动学生 VM，连接到该 VM，停止该 VM，然后删除该 VM。 可以在此页上启动这些 VM，也可以让你的学生启动它们。 

    ![处于已停止状态的虚拟机](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以管理员身份创建并管理实验室帐户](how-to-manage-lab-accounts.md)
- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)

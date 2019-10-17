---
title: 在 Azure 实验室服务中启用多 VM 环境 |Microsoft Docs
description: 了解如何在 Azure 实验室服务的教室实验室中的模板虚拟机内创建具有多个虚拟机的环境。
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
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332319"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>在课堂实验室模板 VM 中创建具有多个 Vm 的环境
目前，Azure 实验室服务可让你在实验室中设置一个模板虚拟机，并为每个用户提供单个副本。 但如果你是教授教授 IT 类如何设置防火墙或服务器的教授，你可能需要为每个学生提供一个环境，在此环境中，多个虚拟机可以通过网络彼此通信。

利用嵌套虚拟化，你可以在实验室模板虚拟机内创建多 VM 环境。 发布模板将在实验室中为每个用户提供一个虚拟机，并在其中设置多个虚拟机。

## <a name="what-is-nested-virtualization"></a>什么是嵌套虚拟化？
利用嵌套虚拟化，你可以在虚拟机中创建虚拟机。 嵌套虚拟化是通过 Hyper-v 完成的，并且仅适用于 Windows Vm。

有关嵌套虚拟化的详细信息，请参阅以下文章：

- [Azure 中的嵌套虚拟化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [如何在 Azure VM 中启用嵌套虚拟化](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>在 Azure 实验室服务中使用嵌套虚拟化
重要步骤如下：

1. 为实验室创建大小**较大**的**Windows**模板计算机。 
2. 连接到它并[启用嵌套虚拟化](../../virtual-machines/windows/nested-virtualization.md)。


以下过程提供了详细步骤： 

1. 如果尚未创建实验室帐户，请创建一个。 有关说明，请参阅[教程：使用 Azure 实验室服务设置实验室帐户](tutorial-setup-lab-account.md)。
1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 请注意，尚不支持 Internet Explorer 11。 
2. 选择“登录”并输入凭据。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
3. 选择 "**新建实验室**"。 
    
    ![创建课堂实验室](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. 在“新建实验室”窗口中，执行以下操作：： 
    1. 指定实验室的**名称**。 
    2. 为**虚拟机大小**选择 "**大" （嵌套虚拟化）** 或 "**中" （嵌套虚拟化）** 。
    6. 选择要使用的 Windows**映像**。 嵌套虚拟化仅适用于 Windows 计算机。 
    4. 然后，选择“下一步”。 

        ![创建课堂实验室](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. 在 "**虚拟机凭据**" 页上，为实验室中的所有 vm 指定默认凭据。 指定用户的 "**名称**" 和 "**密码**"，然后选择 "**下一步**"。  

        ![新建实验室窗口](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > 记下用户名和密码。 这些信息不会再次显示。
    3. 在 "**实验室策略**" 页上，输入在实验室的计划时间之外为每个用户分配的小时数（**每个用户的配额**），然后选择 "**完成**"。 

        ![每个用户的配额](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. 应会看到以下屏幕，其中显示了模板 VM 创建的状态。 在实验室中创建模板最长需要 20 分钟时间。 

    ![模板 VM 创建状态](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. 在 "**模板**" 页上，选择工具栏上的 "**自定义模板**"。 

    ![自定义模板按钮](../media/how-to-create-manage-template/customize-template-button.png)
2. 在 "**自定义模板**" 对话框中，选择 "**继续**"。 一旦你启动了模板并进行了更改，它将不再与上次发布到用户的虚拟机具有相同的设置。 在再次发布后，模板更改将不会反映在用户的现有虚拟机上。

    !["自定义" 对话框](../media/how-to-create-manage-template/customize-template-dialog.png)
1. 在工具栏上选择 "**连接到模板**" 按钮，连接到模板 VM 以配置嵌套虚拟化。，并按照说明进行操作。 如果是 Windows 计算机，你将看到一个用于下载 RDP 文件的选项。 

    ![连接到模板 VM](../media/how-to-create-manage-template/connect-template-vm.png) 
9. 在模板虚拟机内部，设置嵌套虚拟化，并配置包含多个虚拟机的虚拟网络。 有关详细的分步说明，请参阅[如何在 AZURE VM 中启用嵌套虚拟化](../../virtual-machines/windows/nested-virtualization.md)。 下面是这些步骤的快速摘要： 
    1. 启用模板虚拟机中的 Hyper-v 功能。
    2. 使用嵌套虚拟机的 internet 连接设置内部虚拟网络
    3. 通过 Hyper-v 管理器创建虚拟机
    4. 为虚拟机分配 IP 地址
10. 在 "**模板**" 页上，选择工具栏上的 "**发布**"。 

    ![发布模板按钮](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 发布后无法取消发布。 
8. 在 "**发布模板**" 页上，输入要在实验室中创建的虚拟机的数量，然后选择 "**发布**"。 

    ![发布模板-Vm 数](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. 你将在页面上看到 "发布模板" 的**状态**。 此过程最长可能需要花费一小时。 

    ![发布模板 - 进度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>后续步骤

现在，每个用户都会获得一个虚拟机，其中包括其中的多 VM 环境。 若要了解如何将用户添加到实验室并向其发送注册链接，请参阅以下文章：[将用户添加到实验室](tutorial-setup-classroom-lab.md#add-users-to-the-lab)。
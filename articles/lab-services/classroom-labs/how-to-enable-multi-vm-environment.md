---
title: 启用多 VM 环境中 Azure 实验室服务 |Microsoft Docs
description: 了解如何使用 Azure 实验室服务教室实验室中的模板虚拟机中的多个虚拟机创建环境。
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190513"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>使用模板的教室实验室 VM 内的多个 Vm 创建环境
当前 Azure 实验室服务，可将一个模板在实验室中的虚拟机设置，使单个副本可用于每个用户。 但如果你是教授如何设置防火墙或服务器上的 IT 课程的教授，您可能需要为每个您的学生提供在其中多个虚拟机可以相互通信通过网络环境。

嵌套虚拟化，可创建多 VM 环境在实验室的虚拟机模板。 发布模板将会提供与在其中的多个 Vm 设置虚拟机在实验室中的每个用户。

## <a name="what-is-nested-virtualization"></a>什么是嵌套虚拟化？
嵌套虚拟化，可创建虚拟机中的虚拟机。 嵌套虚拟化是通过 HYPER-V，且仅可在 Windows Vm 上。

有关嵌套虚拟化的详细信息，请参阅以下文章：

- [在 Azure 中的嵌套虚拟化](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [如何启用 Azure VM 中的嵌套虚拟化](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>在 Azure 实验室服务中使用嵌套虚拟化
重要的步骤如下：

1. 创建**大型**大小**Windows**模板计算机实验室。 
2. 连接到它并[启用嵌套虚拟化](../../virtual-machines/windows/nested-virtualization.md)。


以下过程为您提供了详细的步骤： 

1. 如果你还没有，创建实验室帐户。 有关说明，请参阅[教程：设置实验室帐户通过 Azure 实验室服务](tutorial-setup-lab-account.md)。
2. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 
3. 选择“登录”并输入凭据。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
4. 在“新建实验室”窗口中，执行以下操作：： 
    1. 指定实验室的**名称**。 
    2. 在实验室中指定最大**虚拟机数**。 可以在创建实验室后增加或减少 VM 数，也可以在现有实验室中进行。 有关详细信息，请参阅[更新实验室中的 VM 数](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)
    6. 选择“保存”。

        ![创建课堂实验室](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. 在“选择虚拟机规范”页上执行以下步骤：
    1. 选择**大型**虚拟机 (Vm)，以便在实验室中创建的大小。 目前，大尺寸的仅支持嵌套虚拟化。
    2. 选择的虚拟机映像**Windows 映像**。 嵌套虚拟化才可在 Windows 计算机上。 
    3. 选择“**下一步**”。

        ![指定 VM 规范](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
5. 在“设置凭据”页上，指定实验室中所有 VM 的默认凭据。 
    1. 指定实验室中所有 VM 的**用户名**。
    2. 指定用户的**密码**。 

        > [!IMPORTANT]
        > 记下用户名和密码。 这些信息不会再次显示。
    3. 选择“创建”。 

        ![设置凭据](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. 在“配置模板”页上，查看实验室创建过程的状态。 在实验室中创建模板最长需要 20 分钟时间。 

    ![配置模板](../media/tutorial-setup-classroom-lab/configure-template.png)
7. 配置完模板后，会看到以下页： 

    ![完成配置后的“配置模板”页](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. 上**配置模板**页上，选择**Connect**连接到 VM，以便配置嵌套虚拟化的模板。 您还可以配置更高版本后完成此向导中的步骤。 
9. 模板虚拟机，内部设置嵌套虚拟化和使用多个虚拟机配置虚拟网络。 有关详细的分步说明，请参阅[如何启用 Azure VM 中的嵌套虚拟化](../../virtual-machines/windows/nested-virtualization.md)。 下面是这些步骤的快速摘要： 
    1. 启用模板在虚拟机中的 HYPER-V 功能。
    2. 使用嵌套的虚拟机的 internet 连接设置的内部虚拟网络
    3. 创建虚拟机上通过 HYPER-V 管理器
    4. 将 IP 地址分配给虚拟机
10. 在模板页上选择“下一步”。 
11. 在“发布模板”页上执行以下操作。 
    1. 若要立即发布模板，请选择“发布”。  

        > [!WARNING]
        > 发布后无法取消发布。 
    2. 若要在以后发布，请选择“保存供以后发布”。 可以在完成向导后发布模板 VM。 有关如何在完成向导后进行配置和发布的详细信息，请参阅[如何管理课堂实验室](how-to-manage-classroom-labs.md)一文中的[发布模板](how-to-create-manage-template.md#publish-the-template-vm)部分。

        ![发布模板](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. 可以查看**发布模板的进度**。 此过程最长可能需要花费一小时。 

    ![发布模板 - 进度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 成功发布模板后，会看到以下页。 选择“完成”。

    ![发布模板 - 成功](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 实验室的**仪表板**随即显示。 
    
    ![课堂实验室仪表板](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>后续步骤

现在，每个用户获取单个虚拟机包含其中的多 VM 环境。 若要了解如何将用户添加到实验室，并向其发送注册链接，请参阅以下文章：[将用户添加到实验室](tutorial-setup-classroom-lab.md#add-users-to-the-lab)。
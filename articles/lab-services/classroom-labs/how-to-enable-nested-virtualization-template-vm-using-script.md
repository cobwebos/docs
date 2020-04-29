---
title: 在 Azure 实验室服务（脚本）中的模板 VM 上启用嵌套虚拟化 |Microsoft Docs
description: 了解如何在内部创建包含多个 Vm 的模板 VM。  换句话说，在 Azure 实验室服务中的模板 VM 上启用嵌套虚拟化。
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
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 56e5ad21f94521565b4df193b2450a1c994b66f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503031"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>使用脚本在 Azure 实验室服务中的模板虚拟机上启用嵌套虚拟化

利用嵌套虚拟化，你可以在实验室模板虚拟机内创建多 VM 环境。 发布模板将在实验室中为每个用户提供一个虚拟机，并在其中设置多个虚拟机。  有关嵌套虚拟化和 Azure 实验室服务的详细信息，请参阅[在 Azure 实验室服务中的模板虚拟机上启用嵌套虚拟化](how-to-enable-nested-virtualization-template-vm.md)。

本文中的步骤重点介绍如何为 Windows Server 2016 或 Windows Server 2019 设置嵌套虚拟化。 你将使用脚本通过 Hyper-v 设置模板计算机。  以下步骤将指导你完成如何使用[实验室服务 hyper-v 脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)。

>[!IMPORTANT]
>创建实验室时，为虚拟机大小选择 "**大" （嵌套虚拟化）** 或 "**中" （嵌套虚拟化）** 。  否则，嵌套虚拟化将不起作用。  

## <a name="run-script"></a>运行脚本

1. 如果使用的是 Internet Explorer，则可能需要将添加`https://github.com`到 "受信任的站点" 列表中。
    1. 打开 Internet Explorer。
    1. 选择齿轮图标，然后选择 " **Internet 选项**"。  
    1. 出现 " **Internet 选项**" 对话框时，选择 "**安全性**"，选择 "**受信任的站点**"，单击 "**站点**" 按钮。
    1. 当 "**受信任的站点**" 对话框`https://github.com`出现时，添加到 "受信任的网站" 列表，然后选择 "**关闭**"。

        ![受信任的站点](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. 按照以下步骤所述下载 Git 存储库文件。
    1. 请参阅[https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)。
    1. 单击 "**克隆或下载**" 按钮。
    1. 单击 "**下载 ZIP**"。
    1. 提取 ZIP 文件

    >[!TIP]
    >你还可以在[https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)中克隆 Git 存储库。

1. 在**管理员**模式下启动**PowerShell** 。
1. 在 PowerShell 窗口中，导航到下载了该脚本的文件夹。 如果要从存储库文件的顶部文件夹进行导航，则该脚本位于`azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`。
1. 您可能必须更改执行策略才能成功运行该脚本。 运行以下命令：

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. 运行以下脚本：

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > 该脚本可能需要重新启动计算机。 按照脚本中的说明操作，然后重新运行该脚本，直到在输出中显示**脚本 "已完成**"。
1. 请不要忘记重置执行策略。 运行以下命令：

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>结束语

现在，你的模板计算机已准备好创建 Hyper-v 虚拟机。 有关如何创建 Hyper-v 虚拟机的说明，请参阅[在 hyper-v 中创建虚拟机](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)。 另请参阅[Microsoft 评估中心](https://www.microsoft.com/evalcenter/)，查看可用的操作系统和软件。  

## <a name="next-steps"></a>后续步骤

下一步是设置任何实验室的常见步骤。

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [向学生发送电子邮件注册链接](how-to-configure-student-usage.md#send-invitations-to-users)
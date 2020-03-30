---
title: 在 Azure 实验室服务（脚本）中的模板 VM 上启用嵌套虚拟化 |微软文档
description: 了解如何创建包含多个 VM 的模板 VM。  换句话说，在 Azure 实验室服务中的模板 VM 上启用嵌套虚拟化。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503031"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>使用脚本在 Azure 实验室服务中的模板虚拟机上启用嵌套虚拟化

嵌套虚拟化使您能够在实验室的模板虚拟机内创建多 VM 环境。 发布模板将为实验室中的每个用户提供一个虚拟机，其中设置多个 VM。  有关嵌套虚拟化和 Azure 实验室服务的详细信息，请参阅在[Azure 实验室服务中的模板虚拟机上启用嵌套虚拟化](how-to-enable-nested-virtualization-template-vm.md)。

本文中的步骤侧重于为 Windows Server 2016 或 Windows Server 2019 设置嵌套虚拟化。 您将使用脚本设置具有 Hyper-V 的模板计算机。  以下步骤将指导您了解如何使用[实验室服务 Hyper-V 脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)。

>[!IMPORTANT]
>创建实验室时，为虚拟机大小选择 **"大型（嵌套虚拟化）"** 或 **"中型"（嵌套虚拟化）。**  嵌套虚拟化将不起作用。  

## <a name="run-script"></a>运行脚本

1. 如果您使用的是 Internet 资源管理器，您可能需要添加到`https://github.com`受信任的网站列表中。
    1. 打开 Internet Explorer。
    1. 选择齿轮图标，然后选择**互联网选项**。  
    1. 此时显示 **"互联网选项"** 对话框，选择 **"安全**"，选择 **"受信任的网站**"，单击 **"站点"** 按钮。
    1. 当"**受信任的网站"** 对话框出现时，`https://github.com`将添加到受信任的网站列表，然后选择"**关闭**"。

        ![受信任的站点](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. 下载以下步骤中概述的 Git 存储库文件。
    1. 转到[https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)。
    1. 单击 **"克隆"或"下载"** 按钮。
    1. 单击**下载 ZIP**。
    1. 提取 ZIP 文件

    >[!TIP]
    >您还可以在 中[https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)克隆 Git 存储库。

1. 在**管理员**模式下启动**电源外壳**。
1. 在 PowerShell 窗口中，使用下载的脚本导航到文件夹。 如果要从存储库文件的顶部文件夹导航，则脚本位于`azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`。
1. 您可能需要更改执行策略才能成功运行脚本。 运行以下命令：

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. 运行以下脚本：

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > 脚本可能需要重新启动计算机。 按照脚本的说明重新运行脚本，直到在输出中看到**脚本完成**为止。
1. 不要忘记重置执行策略。 运行以下命令：

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>结束语

现在，您的模板计算机已准备好创建超 V 虚拟机。 有关如何创建超 V 虚拟机的说明，请参阅[在 Hyper-V 中创建](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)虚拟机。 此外，请参阅[Microsoft 评估中心](https://www.microsoft.com/evalcenter/)查看可用的操作系统和软件。  

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室的常见步骤。

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置计划](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [电子邮件注册链接给学生](how-to-configure-student-usage.md#send-invitations-to-users)
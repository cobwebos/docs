---
title: 在 Azure 实验室服务中的模板 VM 上启用嵌套虚拟化 (脚本) |Microsoft Docs
description: 了解如何使用脚本在内部创建包含多个 Vm 的模板 VM。  也就是说，在 Azure 实验室服务中对模板 VM 启用嵌套虚拟化。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5ae50bd11ab9a8adb769920f6d473a2ff2ce9342
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91251489"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>使用脚本在 Azure 实验室服务中的模板虚拟机上启用嵌套虚拟化

使用嵌套虚拟化，可以在实验室的模板虚拟机内创建多 VM 环境。 发布模板会为实验室中的每个用户提供一个虚拟机，其中包含多个 VM。  有关嵌套虚拟化和 Azure 实验室服务的详细信息，请参阅 [在 Azure 实验室服务中的模板虚拟机上启用嵌套虚拟化](how-to-enable-nested-virtualization-template-vm.md)。

本文中的步骤重点介绍如何为 Windows Server 2016、Windows Server 2019 或 Windows 10 设置嵌套虚拟化。 你将使用脚本通过 Hyper-v 设置模板计算机。  以下步骤将指导你完成如何使用 [实验室服务 hyper-v 脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)。

>[!IMPORTANT]
>创建实验室时，选择“大型(嵌套虚拟化)”或“中等(嵌套虚拟化)”作为虚拟机大小。  否则，嵌套虚拟化将不起作用。  

## <a name="run-script"></a>运行脚本

1. 如果使用的是 Internet Explorer，则可能需要将添加 `https://github.com` 到 "受信任的站点" 列表中。
    1. 打开 Internet Explorer。
    1. 选择齿轮图标，然后选择 " **Internet 选项**"。  
    1. 出现 " **Internet 选项** " 对话框时，选择 " **安全性**"，选择 " **受信任的站点**"，单击 " **站点** " 按钮。
    1. 当 " **受信任的站点** " 对话框出现时，添加 `https://github.com` 到 "受信任的网站" 列表，然后选择 " **关闭**"。

        ![受信任的站点](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. 按照以下步骤所述下载 Git 存储库文件。
    1. 请参阅  [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/) 。
    1. 单击 " **克隆或下载** " 按钮。
    1. 单击 " **下载 ZIP**"。
    1. 提取 ZIP 文件

    >[!TIP]
    >你还可以在中克隆 Git 存储库 [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git) 。

1. 在**管理员**模式下启动**PowerShell** 。
1. 在 PowerShell 窗口中，导航到下载了该脚本的文件夹。 如果要从存储库文件的顶部文件夹进行导航，则该脚本位于 `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` 。
1. 您可能必须更改执行策略才能成功运行该脚本。 运行以下命令：

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. 运行以下脚本：

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > 该脚本可能需要重新启动计算机。 按照脚本中的说明操作，然后重新运行该脚本，直到在输出中显示 **脚本 "已完成** "。
1. 请不要忘记重置执行策略。 运行以下命令：

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>结束语

现在，你的模板计算机已准备好创建 Hyper-v 虚拟机。 有关如何创建 Hyper-v 虚拟机的说明，请参阅 [在 hyper-v 中创建虚拟机](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) 。 另请参阅 [Microsoft 评估中心](https://www.microsoft.com/evalcenter/) ，查看可用的操作系统和软件。  

## <a name="next-steps"></a>后续步骤

下一步是设置任何实验室的常见步骤。

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [向学生发送电子邮件注册链接](how-to-configure-student-usage.md#send-invitations-to-users)
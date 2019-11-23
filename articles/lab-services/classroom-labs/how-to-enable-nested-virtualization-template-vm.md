---
title: 在 Azure 实验室服务中对模板 VM 启用嵌套虚拟化 |Microsoft Docs
description: 了解如何在 Azure 实验室服务中的模板 VM 上启用嵌套虚拟化。
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
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981990"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>在 Azure 实验室服务中的模板虚拟机上启用嵌套虚拟化
本文介绍如何在 Azure 实验室服务中的模板虚拟机上设置嵌套虚拟化。 当类中的每个学生都需要多台计算机时，可在 Azure 实验室服务中使用嵌套虚拟化。
 
## <a name="considerations"></a>注意事项
使用嵌套虚拟化设置实验室之前，需注意以下几个事项。

- 创建新实验室时，为虚拟机大小选择 "**中" （嵌套虚拟化）** 或**大**大小。 这些虚拟机大小支持嵌套虚拟化。 
- 选择将为主机和客户端虚拟机提供良好性能的大小。  请记住，在使用虚拟化时，所选的大小必须足以满足一台计算机的需要，但必须同时满足主机和必须同时运行的所有客户端计算机。
- 客户端虚拟机将无法访问 azure 资源，如 Azure 虚拟网络上的 DNS 服务器。
- 主机虚拟机要求安装程序允许客户端计算机具有 internet 连接。 
- 客户端虚拟机被授权为独立计算机。 有关 Microsoft 操作系统和产品许可的信息，请参阅[Microsoft 许可](https://www.microsoft.com/licensing/default)。 在设置模板计算机之前，请检查正在使用的任何其他软件的许可协议。

## <a name="enable-nested-virtualization-on-a-template-vm"></a>在模板 VM 上启用嵌套虚拟化
本部分中的步骤重点介绍如何为 Windows Server 2016 或 Windows Server 2019 设置嵌套虚拟化。 你将使用脚本通过 Hyper-v 设置模板计算机。 有关自动解决方案，请参阅[实验室服务版 Hyper-v 脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)中的脚本。 以下步骤将引导你完成如何使用该脚本。

1. 如果使用的是 Internet Explorer，则可能需要将 `https://github.com` 添加到 "受信任的站点" 列表中。 
    1. 打开 Internet Explorer。
    1. 选择齿轮图标，然后选择 " **Internet 选项**"。  
    1. 出现 " **Internet 选项**" 对话框时，选择 "**安全性**"，选择 "**受信任的站点**"，单击 "**站点**" 按钮。
    1. 当 "**受信任的站点**" 对话框出现时，将 `https://github.com` 添加到受信任的网站列表中，然后选择 "**关闭**"。

        ![受信任的站点](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  按照以下步骤所述下载 Git 存储库文件。  此外，也可以从[https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)中克隆 Git 存储库。 
    1. 转到 [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)。
    1. 单击 "**克隆或下载**" 按钮。
    1. 单击 "**下载 ZIP**"。
    1. 提取 ZIP 文件
1. 在**管理员**模式下启动**PowerShell** 。
1. 在 PowerShell 窗口中，导航到下载了该脚本的文件夹。 如果要从存储库文件的顶部文件夹进行导航，则该脚本位于 `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`。
1. 您可能必须更改执行策略才能成功运行该脚本。 运行以下命令：
    
    ```powershell
    Set-ExecutionPolicy bypass -force 
    ```
1. 运行脚本：
    
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
请参阅以下文章：

- [如何在 Azure VM 中启用嵌套虚拟化](../../virtual-machines/windows/nested-virtualization.md) 
- [在 Windows Server 上安装 Hyper-v 角色](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [实验室服务 Hyper-v 脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)

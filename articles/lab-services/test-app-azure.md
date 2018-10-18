---
title: 如何在 Azure 中测试应用 | Microsoft Docs
description: 了解如何在实验室中创建文件共享并将其装载到实验室中的本地计算机和虚拟机，然后将桌面/Web 应用程序部署到该文件共享并对其进行测试。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: spelluru
ms.openlocfilehash: 099bdc25c27e264c3c7732243068307856840409
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "44030293"
---
# <a name="test-your-app-in-azure"></a>在 Azure 中测试应用 
本文提供有关使用开发测试实验室在 Azure 中测试应用程序的步骤。 首先，在实验室中设置一个文件共享，并将其作为驱动器装载到实验室中的本地开发计算机和 VM 上。 然后，使用 Visual Studio 2017 将应用部署到文件共享，以便可以在实验室中的 VM 上运行该应用。  

## <a name="prerequisites"></a>先决条件 
1. [创建一个 Azure 订阅](https://azure.microsoft.com/free/)（如果没有订阅），并登录到 [Azure 门户](https://portal.azure.com)。
2. 遵照[此文](devtest-lab-create-lab.md)中的说明，使用 Azure 开发测试实验室创建一个实验室。 将该实验室固定到仪表板，以便下次登录时可以轻松找到它。 使用 Azure 开发测试实验室可在 Azure 中快速创建资源，同时可以最大程度地减少浪费和控制成本。 有关开发测试实验室的详细信息，请参阅[概述](devtest-lab-overview.md)。 
3. 遵照[创建存储帐户](../storage/common/storage-create-storage-account.md)一文中的说明，在实验室的资源组中创建一个 Azure 存储帐户。 在“创建存储帐户”页上，为“资源组”选择“使用现有项”，然后选择“实验室的资源组”。 
4. 遵照[在 Azure 文件中创建文件共享](../storage/files/storage-how-to-create-file-share.md)一文中的说明，在 Azure 存储中创建文件共享。 

## <a name="mount-the-file-share-on-your-local-machine"></a>在本地计算机上装载文件共享
1. 在本地计算机上，使用[在 Windows 中使用 Azure 文件共享](../storage/files/storage-how-to-use-files-windows.md)一文的[在 Windows 中保存 Azure 文件共享凭据](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows)部分所述的脚本。 
2. 然后，使用 `net use` 命令将文件共享装载到计算机上。 示例命令如下：运行该命令之前，请指定 Azure 存储名称和文件共享名称。 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>在实验室中创建 VM
1. 在“文件共享”页上，选择顶部痕迹导航菜单中的“资源组”。 此时会显示“资源组”页。 
    
    ![在痕迹导航菜单中选择“资源组”](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. 在“资源组”页上，选择在开发测试实验室中创建的**实验室**。

    ![选择实验室](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. 在实验室的“开发测试实验室”页上，选择工具栏中的“+ 添加”。 

    ![实验室“添加”按钮](media/test-app-in-azure/add-button-in-lab.png)
4. 在“选择基础操作系统”页上搜索 **smalldisk**，然后选择“[smalldisk] Windows Server 2016 Data Center”。 

    ![选择小磁盘 Windows Server](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. 在“虚拟机”页上，指定“虚拟机名称”、“用户名”和“密码”，然后选择“创建”。    
    
    ![创建虚拟机的页](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>在 VM 上装载文件共享
1. 成功创建虚拟机后，在列表中选择该**虚拟机**。    

    ![选择实验室 VM](media/test-app-in-azure/select-lab-vm.png)
2. 在工具栏上选择“连接”以连接到 VM。 
3. 在“命令行工具”部分使用“Windows 安装”链接来[安装 Azure PowerShell](https://azure.microsoft.com/downloads/)。 有关安装 Azure PowerShell 的其他方法，请参阅[此文](/powershell/azure/install-azurerm-ps?view=azurermps-6.8.1)。
4. 遵照[装载文件共享](#mount-the-file-share)部分的说明操作。 

## <a name="publish-your-app-from-visual-studio"></a>从 Visual Studio 发布应用
本部分介绍如何将应用从 Visual Studio 发布到云中的测试 VM。

1. 使用 Visual Studio 2017 创建桌面/Web 应用程序。
2. 生成应用。
3. 若要发布应用，请在“解决方案资源管理器”中右键单击相应的项目，然后选择“发布”。 
4. 在“发布向导”中，输入映射到文件共享的**驱动器**。

    **桌面应用：**

    ![桌面应用](media/test-app-in-azure/desktop-app.png)

    **Web 应用：**

    ![Web 应用](media/test-app-in-azure/web-app.png)

1. 选择“下一步”以完成发布工作流，然后选择“完成”。 完成向导步骤后，Visual Studio 会生成应用程序，并将其发布到文件共享。 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>在实验室中的测试 VM 上测试应用

1. 导航到实验室中 VM 的“虚拟机”页。 
2. 在工具栏上选择“启动”以启动 VM（如果它处于停止状态）。 可以针对 VM 设置自动启动和自动关闭策略，以免每次测试时都要启动和停止。 
3. 选择“连接”。

    ![虚拟机页](media/test-app-in-azure/virtual-machine-page.png)
4. 在虚拟机中启动“文件资源管理器”，然后选择“此电脑”找到自己的文件共享。

    ![在 VM 上找到共享](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > 如果出于任何原因在虚拟机或本地计算机上找不到自己的文件共享，可以运行 `net use` 命令来重新装载该文件共享。 在 Azure 门户上，你的**文件共享**的“连接”向导中提供了 `net use` 命令。
1. 打开文件共享，并确认看到了从 Visual Studio 部署的应用。 

    ![在 VM 上打开共享](media/test-app-in-azure/open-file-share.png)

    现在，可以在 Azure 上创建的测试 VM 中访问和测试你的应用。

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解如何在实验室中使用 VM。 

- [将 VM 添加到实验室](devtest-lab-add-vm.md)
- [重启实验室 VM](devtest-lab-restart-vm.md)
- [调整实验室 VM 的大小](devtest-lab-resize-vm.md)

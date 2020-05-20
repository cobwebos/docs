---
title: 访问 Azure 开发测试实验室中的实验室 | Microsoft Docs
description: 在本教程中，访问使用 Azure 开发测试实验室创建的实验室，认领虚拟机、使用它们然后取消认领。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/18/2019
ms.author: spelluru
ms.openlocfilehash: ee9a68df685095244fc9471b7d4ab0f6cee0642d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "68360260"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>教程：访问 Azure 开发测试实验室中的实验室
本教程使用[教程：在 Azure 开发测试实验室中创建实验室](tutorial-create-custom-lab.md)中创建的实验室。

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 认领实验室中的虚拟机 (VM)
> * 连接到 VM
> * 取消认领 VM

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="access-the-lab"></a>访问实验室

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中选择“所有资源”  。 
3. 为资源类型选择“开发测试实验室”  。 
4. 选择实验室。 

    ![选择实验室](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>认领 VM

1. 在“可认领虚拟机”列表中，选择“...”（省略号），然后选择“认领计算机”    。

    ![认领虚拟机](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. 确认在“我的虚拟机”列表中看见了 VM  。

    ![我的虚拟机](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>连接到 VM

1. 在列表中选择 VM。 可看到 VM 的“虚拟机页”  。 选择工具栏上的“连接”  。

    ![连接到虚拟机](./media/tutorial-use-custom-lab/connect-button.png)
2. 将下载的“RDP”文件保存在硬盘中，然后用它来连接虚拟机  。 指定上一节创建 VM 时提到的用户名和密码。 

    若要连接到 Linux VM，必须为该 VM 启用 SSH 和/或 RDP 访问权限。 如需通过 RDP 连接到 Linux VM 的步骤，请参阅[安装和配置远程桌面以连接到 Azure 中的 Linux VM](../virtual-machines/linux/use-remote-desktop.md)。 

    > [!NOTE]
    > 还可以通过其他方式来访问 VM 的“虚拟机”页。 以下是其中的一些方式： 
    > 
    > 1. 搜索订阅中的所有 VM。 在 VM 列表中选择你的 VM 以进入“虚拟机”  页。
    > 2. 导航到资源组的“资源组”  页。 然后，从资源组中的资源列表中选择你的 VM 以进入“虚拟机”  页。 
    >
    > 请勿使用通过这些选项进入的“虚拟机”页上工具栏中的“连接”按钮。 而应从“开发测试实验室”页导航到“虚拟机”页（如本文所示），然后使用工具栏上的“连接”按钮。


## <a name="unclaim-the-vm"></a>取消认领 VM
使用 VM 完成操作后，执行以下步骤取消认领 VM： 

1. 在虚拟机页上，选择工具栏上的“取消认领”  。 

    ![取消认领 VM](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. 取消认领之前 VM 处于关闭状态。 可以在通知中查看此操作的状态。  
3. 单击顶部痕迹导航菜单中的实验室名称，导航回“开发测试实验室”页。 
    
    ![导航回实验室](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. 确认在底部的“可认领的虚拟机”列表中看到 VM。 

    
## <a name="next-steps"></a>后续步骤
本教程演示了如何访问和使用通过 Azure 开发测试实验室创建的实验室。 有关在实验室中访问和使用 VM 的详细信息，请参阅 

> [!div class="nextstepaction"]
> [如何：在实验室中使用 VM](devtest-lab-add-vm.md)


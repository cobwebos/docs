---
title: 访问 Azure 开发测试实验室中的自定义实验室 | Microsoft Docs
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
ms.date: 04/09/2018
ms.author: spelluru
ms.openlocfilehash: ce4522673bac56f73944413d102b7cb36cf93f30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-access-a-custom-lab-in-azure-devtest-labs"></a>教程：访问 Azure 开发测试实验室中的自定义实验室
在本教程中，使用[教程：创建自定义实验室](tutorial-create-custom-lab.md)中创建的自定义实验室。

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 认领自定义实验室中的虚拟机 (VM)
> * 连接到 VM
> * 取消认领 VM

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。

## <a name="access-the-lab"></a>访问实验室

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择左侧菜单上的“所有资源”。 
3. 为资源类型选择“开发测试实验室”。 
4. 选择实验室。 

    ![选择实验室](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>认领 VM

1. 在“可认领虚拟机”列表中，选择“...”（省略号），然后选择“认领计算机”。

    ![认领虚拟机](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. 确认在“我的虚拟机”列表中看见了 VM。

    ![我的虚拟机](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>连接到 VM

1. 在列表中选择 VM。 可看到 VM 的“虚拟机页”。 选择工具栏上的“连接”。

    ![连接到虚拟机](./media/tutorial-use-custom-lab/connect-button.png)
2. 将下载的“RDP”文件保存在硬盘中，然后用它来连接虚拟机。 指定上一节创建 VM 时提到的用户名和密码。 

## <a name="unclaim-the-vm"></a>取消认领 VM
使用 VM 完成操作后，执行以下步骤取消认领 VM： 

1. 在虚拟机页上，选择工具栏上的“取消认领”。 

    ![取消认领 VM](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. 取消认领之前 VM 处于关闭状态。 

    ![取消认领状态](./media/tutorial-use-custom-lab/unclaim-status.png) 
1. 取消认领操作完成后，可看到该 VM 位于“可认领虚拟机”列表底部。 
    
## <a name="next-steps"></a>后续步骤
本教程演示了如何访问和使用通过 Azure 开发测试实验室创建的自定义实验室。 有关在自定义实验室中访问和使用 VM 的详细信息，请参阅 

> [!div class="nextstepaction"]
> [如何：在自定义实验室中使用 VM](devtest-lab-add-vm.md)


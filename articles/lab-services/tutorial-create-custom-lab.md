---
title: 使用 Azure 开发测试实验室创建自定义实验室 | Microsoft Docs
description: 在此快速入门中，使用 Azure 开发测试实验室创建自定义实验室。
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
ms.openlocfilehash: df70322a2d6562fce8da17fde652dd0dfbfcaec4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-custom-lab-by-using-azure-devtest-labs"></a>教程：使用 Azure 开发测试实验室设置自定义实验室
在此教程中，使用 Azure 门户创建自定义实验室。 实验室管理员在组织中设置实验室、创建实验室中的 VM 并配置策略。 实验室用户（例如：开发人员和测试人员）认领、连接到并使用实验室中的 VM。 

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 创建自定义实验室
> * 将虚拟机 (VM) 添加到实验室
> * 将用户添加到实验室用户角色

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-custom-lab"></a>创建自定义实验室
以下步骤演示了如何使用 Azure 门户在 Azure 开发测试实验室中创建实验室。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 从左侧的主菜单中，选择“创建资源”（列表顶部），指向“开发人员工具”，然后单击“开发测试实验室”。 

    ![新开发测试实验室菜单](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. 在“创建开发测试实验室”窗口中执行以下操作： 
    1. 在“实验室名称”中，输入实验室的名称。 
    2. 在“订阅”中，选择要在其中创建实验室的订阅。 
    3. 在“资源组”中选择“新建”，然后输入资源组的名称。 
    4. 在“位置”中，选择要在其中创建实验室的位置/区域。 
    5. 选择**创建**。 
    6. 选择“固定到仪表板”。 创建实验室之后，仪表板中会显示实验室。 

        ![创建开发测试实验室的实验室部分](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)

## <a name="add-a-vm-to-the-lab"></a>将 VM 添加到实验室

1. 在“开发测试实验室”页上的工具栏中选择“+ 添加”。 

    ![“添加”按钮](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. 在“选择基”页上，搜索“Ubuntu”关键字并选择列表中的一个基映像。 
1. 在“虚拟机”页上，执行以下操作： 
    1. 在“虚拟机名称”中，输入虚拟机的名称。 
    2. 在“用户名称”中，输入有权访问虚拟机的用户的名称。 
    3. 在“键入值”中，输入用户的密码。 
    4. 选择“高级设置”。
    5. 在“使此计算机可认领”中，选择“是”。
    6. 确认“实例计数”已设为“1”。 如果将其设为 2，将创建 2 个带有名称 `<base image name>00' and <base image name>01` 的 VM。 例如 `win10vm00` 和 `win10vm01`。 
    7. 若要关闭“高级”页，请单击“确定”。 
    8. 选择**创建**。 

        ![选择一个库](./media/tutorial-create-custom-lab/new-virtual-machine.png)
    9. 可在“可认领的虚拟机”列表中查看虚拟机的状态。 创建虚拟机大概需要 25 分钟。 虚拟机会创建在独立的 Azure 资源组中，其名称以具有实验室的当前资源组的名称作为开头。 例如，如果实验室在 `labrg` 中，则可能会在资源组 `labrg3988722144002` 中创建 VM。 

        ![VM 创建状态](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. 创建 VM 之后，可在列表中的“可认领的虚拟机”列表中进行查看。 

## <a name="add-a-user-to-the-lab-user-role"></a>将用户添加到实验室用户角色

1. 在左侧菜单中，选择“配置和策略”。 

    ![配置和策略](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. 从菜单中选择“访问控制 (IAM)”，并在工具栏中选择“+ 添加”。 

    ![访问控制 - 添加用户按钮](./media/tutorial-create-custom-lab/access-control-add.png)
1. 在“添加权限”页上，执行以下操作：
    1. 在“角色”中，选择“开发测试实验室用户”。 
    2. 选择要添加的“用户”。 
    3. 选择“保存”。

        ![添加权限](./media/tutorial-create-custom-lab/add-lab-user.png)
4. 若要关闭“配置和策略 - 访问控制 (IAM)”，请选择右上角的“X”。 

## <a name="cleanup-resources"></a>清理资源
下一教程演示实验室用户如何认领和连接实验室中的 VM。 如果不想完成该教程，并想清理作为本教程一部分而创建的资源，请执行下列步骤： 

1. 在 Azure 门户中，选择菜单中的**资源组**。 
2. 选择创建了实验室的资源组。 
3. 从工具栏选择**删除资源组**。 删除资源组将删除组中的所有资源（包括实验室）。 
4. 重复这些步骤，以删除所创建的名为 `<your resource group name><random numbers>` 的其他资源组。 例如：`splab3988722144001`。 是在此资源组中创建虚拟机，而不是在具有实验室的所有资源组中创建。 

## <a name="next-steps"></a>后续步骤
在本教程中，创建了带有 VM 的自定义实验室并向一个用户提供了此实验室的访问权限。 若要了解如何作为实验室用户访问实验室，请进入下一教程：

> [!div class="nextstepaction"]
> [教程：访问自定义实验室](tutorial-use-custom-lab.md)


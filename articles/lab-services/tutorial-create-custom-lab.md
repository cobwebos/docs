---
title: 使用 Azure 开发测试实验室创建实验室 | Microsoft Docs
description: 在此教程中，我们使用 Azure 门户在 Azure 开发测试实验室中创建实验室。 实验室管理员会设置实验室、创建实验室中的 VM 并配置策略。
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 44539c6779afaece6d955a907819ef82d8cd7d5a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "79224124"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>教程：使用 Azure 开发测试实验室设置实验室
在此教程中，使用 Azure 门户创建实验室。 实验室管理员在组织中设置实验室、创建实验室中的 VM 并配置策略。 实验室用户（例如：开发人员和测试人员）认领、连接到并使用实验室中的 VM。 

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 创建实验室
> * 将虚拟机 (VM) 添加到实验室
> * 将用户添加到实验室用户角色

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-lab"></a>创建实验室
以下步骤演示了如何使用 Azure 门户在 Azure 开发测试实验室中创建实验室。 

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 从左侧的主菜单中，选择“创建资源”（列表顶部），指向“开发人员工具”，然后单击“开发测试实验室”    。 

    ![新开发测试实验室菜单](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. 在“创建开发测试实验室”窗口中执行以下操作  ： 
    1. 在“实验室名称”中，输入实验室的名称  。 
    2. 在“订阅”中，选择要在其中创建实验室的订阅  。 
    3. 在“资源组”中选择“新建”，然后输入资源组的名称   。 
    4. 在“位置”中，选择要在其中创建实验室的位置/区域  。 
    5. 选择“创建”  。 
    6. 选择“固定到仪表板”  。 创建实验室之后，仪表板中会显示实验室。 

        ![创建开发测试实验室的实验室部分](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)
2. 查看通知，确认实验室是否已成功创建。 选择“转到资源”。   

    ![通知](./media/tutorial-create-custom-lab/creation-notification.png)
3. 确认看到实验室的“开发测试实验室”页。  

    ![实验室主页](./media/tutorial-create-custom-lab/lab-home-page.png)

## <a name="add-a-vm-to-the-lab"></a>将 VM 添加到实验室

1. 在“开发测试实验室”页上的工具栏中选择“+ 添加”   。 

    ![“添加”按钮](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. 在“选择基”页上，使用关键字（例如 Windows、Ubuntu）进行搜索，然后选择列表中的一个基映像  。 
1. 在“虚拟机”页上，执行以下操作  ： 
    1. 在“虚拟机名称”中，输入虚拟机的名称  。 
    2. 在“用户名称”中，输入有权访问虚拟机的用户的名称  。 
    3. 至于“密码”，请输入用户的密码。  

        ![选择一个库](./media/tutorial-create-custom-lab/new-virtual-machine.png)
1. 选择“高级设置”选项卡  。
    1. 在“使此计算机可认领”中，选择“是”   。
    2. 确认“实例计数”已设为“1”   。 如果将其设为 2，将创建 2 个带有名称 `<base image name>00' and <base image name>01` 的 VM。 例如 `win10vm00` 和 `win10vm01`。     
    3. 选择“提交”。  

        ![选择一个库](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
    9. 可在“可认领的虚拟机”列表中查看虚拟机的状态  。 创建虚拟机大概需要 25 分钟。 虚拟机会创建在独立的 Azure 资源组中，其名称以具有实验室的当前资源组的名称作为开头。 例如，如果实验室在 `labrg` 中，则可能会在资源组 `labrg3988722144002` 中创建 VM。 

        ![VM 创建状态](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. 创建 VM 之后，可在列表中的“可认领的虚拟机”列表中进行查看  。 

    > [!NOTE] 
    > 在“高级设置”  页面上，可以为 VM 配置公共、专用或共享 IP 地址。 启用了“共享 IP”  时，Azure 开发测试实验室会自动为 Windows VM 启用 RDP，为 Linux VM 启用 SSH。 如果使用公共 IP  地址创建 VM，则会启用 RDP 和 SSH，而无需开发测试实验室进行任何更改。  

## <a name="add-a-user-to-the-lab-user-role"></a>将用户添加到实验室用户角色

1. 在左侧菜单中，选择“配置和策略”  。 

    ![配置和策略](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. 从菜单中选择“访问控制(IAM)”，并在工具栏中选择“+ 添加角色分配”   。 

    ![添加角色分配 - 按钮](./media/tutorial-create-custom-lab/add-role-assignment-button.png)
1. 在“添加权限”页上，执行以下操作  ：
    1. 在“角色”中，选择“开发测试实验室用户”   。 
    2. 选择要添加的“用户”  。 
    3. 选择“保存”。 

        ![添加用户](./media/tutorial-create-custom-lab/add-user.png)

## <a name="clean-up-resources"></a>清理资源
下一教程演示实验室用户如何认领和连接实验室中的 VM。 如果不想完成该教程，并想清理作为本教程一部分而创建的资源，请执行下列步骤： 

1. 在 Azure 门户中，选择菜单中的**资源组**。 

    ![资源组](./media/tutorial-create-custom-lab/resource-groups.png)
1. 选择创建了实验室的资源组。 
1. 从工具栏选择**删除资源组**。 删除资源组将删除组中的所有资源（包括实验室）。 

    ![实验室资源组](./media/tutorial-create-custom-lab/lab-resource-group.png)
1. 重复这些步骤，以删除所创建的名为 `<your resource group name><random numbers>` 的其他资源组。 例如：`splab3988722144001`。 是在此资源组中创建虚拟机，而不是在具有实验室的所有资源组中创建。 

## <a name="next-steps"></a>后续步骤
在本教程中，创建了带有 VM 的实验室并向一个用户提供了此实验室的访问权限。 若要了解如何作为实验室用户访问实验室，请进入下一教程：

> [!div class="nextstepaction"]
> [教程：访问实验室](tutorial-use-custom-lab.md)


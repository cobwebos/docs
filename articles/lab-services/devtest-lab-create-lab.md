---
title: 在 Azure 开发测试实验室中创建实验室 | Microsoft Docs
description: 在 Azure 开发测试实验室中创建适用于虚拟机的实验室
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: a1e52a8ff7a2018c54c7b88b80bab3c2897b1fb4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建实验室
Azure 开发测试实验室中的实验室是包含一组资源（例如，虚拟机 (VM)）的基础结构，通过指定限制和配额可以更好地管理这些资源。 本文介绍使用 Azure 门户创建实验室的过程。

## <a name="prerequisites"></a>先决条件
若要创建实验室，需要：

* Azure 订阅。 若要了解 Azure 购买选项，请参阅[如何购买 Azure](https://azure.microsoft.com/pricing/purchase-options/) 或[免费试用一个月](https://azure.microsoft.com/pricing/free-trial/)。 必须成为订阅所有者才可以创建实验室。

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建实验室的步骤
以下步骤演示了如何使用 Azure 门户在 Azure 开发测试实验室中创建实验室。 

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 从左侧的主菜单中，选择“所有服务”（位于列表顶部）。

    ![“所有服务”菜单选项](./media/devtest-lab-create-lab/more-services-menu-option.png)

1. 从可用服务列表中，选择“开发测试实验室”。
1. 在“开发测试实验室”区域中，选择“添加”。
   
    ![添加实验室](./media/devtest-lab-create-lab/add-lab-button.png)

1. 在“创建开发测试实验室”下：
   
    1. 为新的实验室输入“实验室名称”  。
    2. 选择要与实验室关联的“订阅”  。
    3. 选择用于存储实验室的“位置”  。
    4. 选择“自动关机”  ，指定是否要自动关闭所有实验室的 VM 以及为其指定参数。 自动关机功能主要是一种节省成本的功能，可以根据需要通过该功能指定何时自动关闭 VM。 可以在创建实验室之后更改自动关机设置，只需按[管理 Azure 开发测试实验室中某个实验室的所有策略](./devtest-lab-set-lab-policy.md#set-auto-shutdown)一文所述步骤操作即可。
    1. 如果要创建自定义标记（将添加到要在实验室中创建的每个资源上），请输入**标记**的**名称**和**值**信息。 标记用于帮助你按类别管理和组织实验室资源。 有关标记的详细信息（包括如何在创建实验室中后添加标记），请参阅[将标记添加到实验室](devtest-lab-add-tag.md)。
    5. 如果想要在门户仪表板上显示实验室的快捷方式，请选择“固定到仪表板”。
    6. 请选择“自动化选项”，以便获取适用于配置自动化的 Azure 资源管理器模板。 
    7. 选择**创建**。 可以通过观察“通知”区域来监视实验室创建过程的状态。 完成后，刷新页面即可在实验室列表中查看新创建的实验室。  
    
    ![创建开发测试实验室的实验室部分](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
实验室创建完成后，以下是一些可以考虑执行的后续步骤：

* [保护对实验室的访问](devtest-lab-add-devtest-user.md)
* [设置实验室策略](devtest-lab-set-lab-policy.md)
* [创建实验室模板](devtest-lab-create-template.md)
* [创建适用于 VM 的自定义项目](devtest-lab-artifact-author.md)
* [将 VM 添加到实验室](devtest-lab-add-vm.md)


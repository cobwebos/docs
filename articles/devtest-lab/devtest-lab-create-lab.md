---
title: "在 Azure 开发测试实验室中创建实验室 | Microsoft Docs"
description: "在 Azure 开发测试实验室中创建适用于虚拟机的实验室"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/30/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 015782373e59d1aaf10a7b089c84c982031b36b2
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---
# <a name="create-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建实验室
Azure 开发测试实验室中的实验室是包含一组资源（例如，虚拟机 (VM)）的基础结构，通过指定限制和配额可以更好地管理这些资源。 本文介绍使用 Azure 门户创建实验室的过程。

## <a name="prerequisites"></a>先决条件
若要创建实验室，需要：

* Azure 订阅。 若要了解 Azure 购买选项，请参阅[如何购买 Azure](https://azure.microsoft.com/pricing/purchase-options/) 或[免费试用一个月](https://azure.microsoft.com/pricing/free-trial/)。 必须成为订阅所有者才可以创建实验室。

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建实验室的步骤
以下步骤演示了如何使用 Azure 门户在 Azure 开发测试实验室中创建实验室。 

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 从左侧的主菜单中，选择“更多服务”（位于列表底部）。

    ![更多服务菜单选项](./media/devtest-lab-create-lab/more-services-menu-option.png)

1. 从可用服务列表中，选择“开发测试实验室”。
1. 在“开发测试实验室”边栏选项卡中，选择“添加”。
   
    ![添加实验室](./media/devtest-lab-create-lab/add-lab-button.png)

1. 在“创建 开发测试实验室”  边栏选项卡上：
   
    1. 为新的实验室输入“实验室名称”  。
    2. 选择要与实验室关联的“订阅”  。
    3. 选择用于存储实验室的“位置”  。
    4. 选择“自动关机”  ，指定是否要自动关闭所有实验室的 VM 以及为其指定参数。 自动关机功能主要是一种节省成本的功能，可以根据需要通过该功能指定何时自动关闭 VM。 可以在创建实验室之后更改自动关机设置，只需按[管理 Azure 开发测试实验室中的某个实验室的所有策略](./devtest-lab-set-lab-policy.md#set-auto-shutdown)一文所述步骤操作即可。
    5. 如果想要在门户仪表板上显示实验室的快捷方式，请选择“固定到仪表板”。
    6. 请选择“自动化选项”，以便获取适用于配置自动化的 Azure Resource Manager 模板。 
    7. 选择“创建” 。 选择“创建”后，将会显示“开发测试实验室”边栏选项卡。 可以通过观察“通知”区域来监视实验室创建过程的状态。 完成后，刷新页面即可在实验室列表中查看新创建的实验室。  
    
    ![创建实验室边栏选项卡](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
实验室创建完成后，以下是一些可以考虑执行的后续步骤：

* [保护对实验室进行的访问](devtest-lab-add-devtest-user.md)。
* [设置实验室策略](devtest-lab-set-lab-policy.md)。
* [创建实验室模板](devtest-lab-create-template.md)。
* [创建适用于 VM 的自定义项目](devtest-lab-artifact-author.md)。
* [将含有项目的 VM 添加到实验室](devtest-lab-add-vm-with-artifacts.md)。



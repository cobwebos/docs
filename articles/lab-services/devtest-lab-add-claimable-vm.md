---
title: 在 Azure 开发测试实验室中的实验室中创建和管理可认领 VM | Microsoft Docs
description: 了解如何向 Azure 开发测试实验室中的实验室添加可声明的虚拟机
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2018
ms.author: spelluru
ms.openlocfilehash: 669dfab75f34a0d1f997dc34f600402d3c10669b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781746"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建和管理可认领 VM
使用与[添加标准 VM](devtest-lab-add-vm.md) 类似的方式向实验室添加可认领 VM：通过作为[自定义映像](devtest-lab-create-template.md)、[公式](devtest-lab-manage-formulas.md)或 [Marketplace 映像](devtest-lab-configure-marketplace-images.md)的“基”添加。 本教程指导你使用 Azure 门户向开发测试实验室中的实验室添加可认领 VM，并展示了认领和取消认领 VM 时需遵循的过程。

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>向 Azure 开发测试实验室中的实验室添加可认领 VM 的步骤
1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 选择“所有服务”，并从列表中选择“开发测试实验室”。
1. 从实验室列表中选择要在其中创建可认领 VM 的实验室。  
1. 在实验室的“概述”页面上，选择“+ 添加”。  

    ![“添加 VM”按钮](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. 在“选择基本映像”区域中，为 VM 选择基本映像。
1. 在“虚拟机”窗格中，在“虚拟机名称”文本框中输入新虚拟机的名称。

    ![实验室 VM 窗格](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. 输入在虚拟机上被授予了管理员权限的**用户名**。  
1. 如果想要使用在[密码存储](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)中存储的密码，请选择“使用保存的密码”，并指定与密码对应的密钥值。 否则，在标签为“键入值”的文本字段输入一个密码。
1. **虚拟机磁盘类型**决定了允许将哪种存储磁盘类型用于实验室中的虚拟机。
1. 选择“虚拟机大小”，并选择指定待创建 VM 的处理器内核、RAM 大小和硬盘驱动器大小的预定义项之一。
1. 选择“项目”，并从项目列表中选择并配置要添加到基础映像的项目。 如果对开发测试实验室或配置项目不熟悉，请参阅[向 VM 中添加现有项目](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)部分，并在完成后返回此处。
1. 选择“高级设置”来配置 VM 的网络选项和到期选项。 在“声明”选项下选择“是”，将虚拟机设为可声明。

  ![进行选择，使 VM 可认领。](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. 如果想要查看或复制 Azure 资源管理器模板，请参阅[保存 Azure 资源管理器模板](devtest-lab-add-vm.md#save-azure-resource-manager-template)部分，并在完成后返回此处。
1. 选择“创建”将指定的 VM 添加到实验室。

   将显示 VM 的创建状态，先是显示为“正在创建”，然后在 VM 启动后显示为“正在运行”。

> [!NOTE]
> 如果使用 [Azure 资源管理器模板](devtest-lab-create-environment-from-arm.md)部署实验室 VM，则可通过将属性部分中的 **allowClaim** 属性设置为 true 来创建可声明的 VM。
>
>

## <a name="using-a-claimable-vm"></a>使用可认领 VM

用户可执行以下某步骤声明“可认领的虚拟机”列表中的任何 VM：

* 从实验室的“概述”页面底部的“可认领虚拟机”列表中，右键单击列表中的某个 VM，并选择“认领虚拟机”。

 ![请求可认领的特定 VM。](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* 在“概述”页面顶部，选择“认领任意虚拟机”。 从“可认领 VM”列表中分配随机的虚拟机。

 ![请求任意可认领 VM。](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


用户认领 VM 后，该 VM 将移动到“我的虚拟机”列表中，且其他任何用户将无法再对其进行认领。

## <a name="unclaim-a-vm"></a>取消认领 VM

如果用户完成了对认领的 VM 的使用并且希望使其可供他人使用，则可以通过执行下列步骤之一将认领的 VM 返还到可认领虚拟机的列表中：

- 从“我的虚拟机”列表中，右键单击列表中的一个虚拟机或选择其省略号（...），然后选择“取消认领”。

  ![在 VM 列表中取消认领 VM。](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- 在“我的虚拟机”列表中，选择一个 VM 以打开其管理窗格，然后从顶部菜单栏中选择“取消认领”。

  ![在 VM 的管理窗格中取消认领 VM。](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

当用户取消认领某个 VM 后，他们不再拥有对该特定实验室 VM 的任何权限。

### <a name="transferring-the-data-disk"></a>转移数据磁盘
如果某个可认领 VM 具有附加到它的数据磁盘，并且用户取消认领了该 VM，则数据磁盘仍然跟随该 VM。 然后，当另一用户认领该 VM 时，该新用户同时认领了该数据磁盘和 VM。

这称为“转移数据磁盘”。 然后，该数据磁盘将出现在新用户的“我的数据磁盘”列表中，以便进行管理。

![取消认领数据磁盘。](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>后续步骤
* 在创建后，可以通过在 VM 的管理窗格上选择“连接”来连接到 VM。
* 探究[开发测试实验室 Azure 资源管理器快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/Samples)。

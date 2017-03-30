---
title: "向 Azure 开发测试实验室中的实验室添加可声明的 VM | Microsoft Docs"
description: "了解如何向 Azure 开发测试实验室中的实验室添加可声明的虚拟机"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: v-craic
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: fde776806c3b5eb126540841c12267ba1a6a90e4
ms.lasthandoff: 03/21/2017


---
# <a name="add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>向 Azure 开发测试实验室中的实验室添加可声明的 VM
使用与[添加标准 VM](devtest-lab-add-vm.md) 类似的方式向实验室添加可声明的 VM：通过作为[自定义映像](devtest-lab-create-template.md)、[公式](devtest-lab-manage-formulas.md)或[应用商店映像](devtest-lab-configure-marketplace-images.md)的“基”添加。 本教程将指导你使用 Azure 门户向开发测试实验室中的实验室添加可声明的 VM，并展示用户声明 VM 时需遵循的过程。

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>向 Azure 开发测试实验室中的实验室添加可声明的 VM 的步骤
1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
1. 从实验室列表中选择要在其中创建 VM 的实验室。  
1. 在实验室的“概述”边栏选项卡上，选择“+ 添加”。  

    ![“添加 VM”按钮](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. 在“选择基本映像”边栏选项卡上为 VM 选择基本映像c。
1. 在“虚拟机”边栏选项卡的“虚拟机名称”文本框中输入新虚拟机的名称。

    ![实验室 VM 边栏选项卡](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. 输入在虚拟机上被授予了管理员权限的**用户名**。  
1. 如果想要使用在[密码存储](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)中存储的密码，请选择“使用保存的密码”，然后指定与密码对应的密钥值。 否则，在标签为“键入值”的文本字段输入一个密码。
1. **虚拟机磁盘类型**决定了允许将哪种存储磁盘类型用于实验室中的虚拟机。
1. 选择“虚拟机大小”，并选择指定待创建 VM 的处理器内核、RAM 大小和硬盘驱动器大小的预定义项之一。
1. 选择“项目”，然后从项目列表中选择并配置要添加到基础映像的项目。
    **注意：**如果对开发测试实验室或配置项目不熟悉，请参阅[向 VM 中添加现有项目](./devtest-lab-add-vm-with-artifacts.md#add-an-existing-artifact-to-a-vm)部分，然后在完成后返回此处。
1. 选择“高级设置”来配置 VM 的网络选项和到期选项。 在“声明”选项下选择“是”，将虚拟机设为可声明。

  ![进行选择，使 VM 可声明。](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. 如果想要查看或复制 Azure Resource Manager 模板，请参阅[保存 Azure Resource Manager 模板](./devtest-lab-add-vm-with-artifacts.md#save-azure-resource-manager-template)部分，然后在完成后返回此处。
1. 选择“创建”将指定的 VM 添加到实验室。
1. 实验室边栏选项卡显示 VM 的创建状态 - 先是显示为“正在创建”，然后当启动 VM 后显示为“正在运行”。

> [!NOTE]
> 如果使用 [Azure Resource Manager 模板](devtest-lab-create-environment-from-arm.md)部署实验室 VM，则可通过将属性部分中的 **allowClaim** 属性设置为 true 来创建可声明的 VM。
>
>

### <a name="using-a-claimable-vm"></a>使用可声明的 VM

用户可执行以下某步骤声明“可声明的虚拟机”列表中的任何 VM：

* 从实验室的“概述”边栏选项卡底部的“可声明的虚拟机”列表中，右键单击列表中的某个 VM，然后选择“声明虚拟机”。

 ![请求可声明的特定 VM。](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* 在“概述”边栏选项卡顶部，选择“声明任意虚拟机”。 从“可声明的 VM”列表中分配随机的虚拟机。

 ![请求任意可声明的 VM。](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


用户声明 VM 后，该 VM 将移动到“我的虚拟机”列表中，且其他用户均无法再对其进行声明。

## <a name="next-steps"></a>后续步骤
* 创建 VM 后，可通过选择 VM 边栏选项卡上的“连接”来连接 VM。
* 浏览[开发测试实验室 Azure Resource Manager 快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)


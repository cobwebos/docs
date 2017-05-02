---
title: "向 Azure 开发测试实验室中的实验室添加 VM | Microsoft 文档"
description: "了解如何向 Azure 开发测试实验室中的实验室添加虚拟机"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 9ac43f4ac0a58ae3326c63ab53259d7621577d34
ms.lasthandoff: 04/19/2017


---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>向 Azure 开发测试实验室中的实验室添加 VM
通过*基本映像*向实验室中添加 VM，该基本映像是[自定义映像](devtest-lab-create-template.md)、[公式](devtest-lab-manage-formulas.md)或[应用商店映像](devtest-lab-configure-marketplace-images.md)。 本教程将引导你完成使用 Azure 门户向开发测试实验室中的实验室添加 VM 的过程。

> [!NOTE]
> [添加可声明的 VM](devtest-lab-add-claimable-vm.md) 演示了如何使 VM 可声明以使其供实验室中的任意用户使用。
>
>

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>向 Azure 开发测试实验室中的实验室添加 VM 的步骤
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
1. 选择“高级设置”来配置 VM 的网络选项和到期选项。 

   若要设置过期选项，请选择“日历”图标，指定一个自动删除 VM 的日期。  默认情况下，VM 永不过期。 
1. 如果想要查看或复制 Azure Resource Manager 模板，请参阅[保存 Azure Resource Manager 模板](./devtest-lab-add-vm-with-artifacts.md#save-azure-resource-manager-template)部分，然后在完成后返回此处。
1. 选择“创建”将指定的 VM 添加到实验室。
1. 实验室边栏选项卡显示 VM 的创建状态 - 先是显示为“正在创建”，然后当启动 VM 后显示为“正在运行”。

## <a name="next-steps"></a>后续步骤
* 创建 VM 后，可通过选择 VM 边栏选项卡上的“连接”来连接 VM。
* 浏览[开发测试实验室 Azure Resource Manager 快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)


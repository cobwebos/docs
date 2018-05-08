---
title: 在 Azure 开发测试实验室的实验室中创建第一个 VM | Microsoft Docs
description: 了解如何在 Azure 开发测试实验室的实验室中创建第一个虚拟机
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0240bbdf2055ac44f8836adfaad3bf6c44893b77
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>在 Azure 开发测试实验室的实验室中创建第一个 VM

最初访问开发测试实验室并要创建第一个 VM 时，可能会使用预先加载的[基本 Marketplace 映像](devtest-lab-configure-marketplace-images.md)。 稍后，创建更多 VM 时，还可以从[自定义映像和公式](devtest-lab-add-vm.md)中选择。 

本教程将引导你完成使用 Azure 门户向开发测试实验室中的实验室添加第一个 VM 的过程。

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>向 Azure 开发测试实验室中的实验室添加第一个 VM 的步骤
1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 选择“所有服务”，并从列表中选择“开发测试实验室”。
1. 从实验室列表中选择要在其中创建 VM 的实验室。  
1. 在实验室的“概述”边栏选项卡上，选择“+ 添加”。  

    ![“添加 VM”按钮](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. 在“选择基本映像”边栏选项卡上为 VM 选择应用商店映像。
1. 在“虚拟机”边栏选项卡的“虚拟机名称”文本框中输入新虚拟机的名称。

    ![实验室 VM 边栏选项卡](./media/devtest-lab-add-vm/devtestlab-lab-add-first-vm.png)

1. 输入在虚拟机上被授予了管理员权限的**用户名**。  
1. 在标签为“键入值”的文本字段中输入密码。
1. **虚拟机磁盘类型**决定了允许将哪种存储磁盘类型用于实验室中的虚拟机。
1. 选择“虚拟机大小”，并选择指定待创建 VM 的处理器内核、RAM 大小和硬盘驱动器大小的预定义项之一。
1. 选择“项目”，并从项目列表中选择并配置要添加到基础映像的项目。
    **注意：**如果对开发测试实验室或配置项目不熟悉，请参阅[向 VM 中添加现有项目](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)部分，并在完成后返回此处。
1. 选择“创建”将指定的 VM 添加到实验室。

   实验室边栏选项卡显示 VM 的创建状态 - 先是显示为“正在创建”，然后在 VM 启动后显示为“正在运行”。

## <a name="next-steps"></a>后续步骤
* 创建 VM 后，可通过选择 VM 边栏选项卡上的“连接”来连接 VM。
* 查看[将 VM 添加到实验室](devtest-lab-add-vm.md)，了解有关在实验室中添加后续 VM 的更完整信息。
* 浏览[开发测试实验室 Azure 资源管理器快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)。

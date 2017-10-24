---
title: "启用 Azure 开发测试实验室中自己的实验室中的已授权映像 | Microsoft Docs"
description: "了解如何使用 Azure 门户在 Azure 开发测试实验室中启用已授权映像"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: tarcher
ms.openlocfilehash: a74eff05285602574e45703dbe5b6caf074adecd
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>启用 Azure 开发测试实验室中自己的实验室中的已授权映像

在 Azure 开发测试实验室中，已授权映像包括条款和条件（通常来自第三方），用户必须接受这些条款和条件，才能在实验室中访问映像。 以下部分描述如何使用已授权映像，以便将其用于创建虚拟机。

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>确定已授权映像是否对用户可用
允许用户从已授权映像创建 VM 的第一步是确保已接受已授权映像的条款和条件。 以下步骤显示如何查看已授权映像的产品/服务状态，必要时接受其条款和条件。

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 选择“更多服务”，并从列表中选择“开发测试实验室”。

1. 从实验室列表中，选择所需的实验室。  

1. 在左侧面板的“设置”下，选择“配置和策略”。

1. 在左侧面板的“虚拟机基础映像”下，选择“Marketplace 映像”。 

    ![Marketplace 映像菜单项](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    将显示所有可用 Marketplace 映像的列表，包括每个映像的“产品/服务状态”。

    ![显示每个映像的产品/服务状态的 Marketplace 映像列表](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    已授权映像显示的产品/服务状态为 
    
    - **已接受条款：**用户可使用已授权映像创建 VM。 
    - **需要评审条款：**用户当前不可使用已授权映像。 实验室用户必须接受许可证的条款和条件，才能将其用于创建 VM。 

## <a name="making-a-licensed-image-available-to-lab-users"></a>使实验室用户能够使用已授权映像
若要确保实验室用户能够使用已授权映像，具有管理员权限的实验室所有者必须首先接受该已授权映像的条款和条件。 启用与已授权映像相关联的订阅的编程部署将自动接受该映像的法律条款和隐私声明。 [Working with Marketplace Images on Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/)（在 Azure 资源管理器上使用 Marketplace 映像）提供了有关 Marketplace 映像的编程部署的其他信息。

可通过执行以下步骤来启用已授权映像的编程部署：

1. 在 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)中的“Marketplace 映像”列表上，确定你希望用户可以访问但尚未接受其条款的已授权映像。 例如，你可能会看到显示“已接受条款”或“需要评审条款”状态的数据科学虚拟机。

    ![配置编程部署窗口](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > 数据科学 VM 是 Azure 虚拟机映像，通过数种常用于数据分析、机器学习和 AI 训练的工具进行预安装、配置和测试。 [适用于 Linux 和 Windows 的 Azure 数据科学虚拟机简介](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)提供了有关 DSVM 的大量信息。
   >
   >

1. 在映像的“产品/服务状态”列中，选择“需要评审条款”。

1. 在“配置编程部署”窗口中，选择“启用”。

    ![配置编程部署窗口](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > 你可能会看到配置编程部署窗口中列出了多个订阅。 请确保仅为目标订阅启用了编程部署。
   >
   >


1. 选择“保存”。 在 Marketplace 映像列表中，该映像现在显示“已接受条款”且可供用户用于创建虚拟机。

## <a name="related-blog-posts"></a>相关的博客文章

- [自定义映像或公式吗？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [复制 Azure 开发测试实验室间的自定义映像](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>后续步骤

- [将 VM 添加到实验室](./devtest-lab-add-vm-with-artifacts.md)

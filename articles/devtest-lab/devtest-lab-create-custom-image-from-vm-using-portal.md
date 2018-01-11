---
title: "基于 VM 创建 Azure 开发测试实验室自定义映像 | Microsoft Docs"
description: "介绍如何通过 Azure 门户在 Azure 开发测试实验室中基于预配的 VM 创建自定义映像"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: v-craic
ms.openlocfilehash: ce854024df5c6974873cf3574bf2de282946f6d0
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="create-a-custom-image-from-a-vm"></a>从 VM 创建自定义映像

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>分步说明

可基于预配的 VM 创建自定义映像，随后使用该自定义映像创建相同的 VM。 以下步骤演示了如何从 VM 创建自定义映像：

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 选择“更多服务”，并从列表中选择“开发测试实验室”。

1. 从实验室列表，选择所需的实验室。  

1. 在实验室边栏选项卡上，选择“虚拟机”。
 
1. 在“虚拟机”边栏选项卡上，选择想要创建自定义映像的 VM。

1. 在虚拟机的边栏选项卡上，选择“创建自定义映像 (VHD)”。

    ![创建自定义映像菜单项](./media/devtest-lab-create-template/create-custom-image.png)

1. 在“创建映像”边栏选项卡，输入名称和自定义映像的说明。 创建 VM 时，此信息显示在基项列表中。

    ![创建自定义映像边栏选项卡](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. 选择是否在 VM 上运行 sysprep。 如果未在 VM 上运行 sysprep，指定从该自定义映像创建 VM 时是否希望运行 sysprep。

1. 创建自定义映像后，选择“确定”。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相关的博客文章

- [自定义映像或公式吗？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [复制 Azure 开发测试实验室间的自定义映像](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>后续步骤

- [将 VM 添加到实验室](devtest-lab-add-vm.md)

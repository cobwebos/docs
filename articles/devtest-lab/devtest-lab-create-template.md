---
title: "基于 VHD 文件创建 Azure 开发测试实验室自定义映像 | Microsoft Docs"
description: "介绍如何使用 Azure 门户基于 VHD 文件在 Azure 开发测试实验室中创建自定义映像"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: v-craic
ms.openlocfilehash: 7605a65d784a9586a4d88625996f4a1c8f154e9d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>从 VHD 文件创建自定义映像

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>分步说明

以下步骤将引导使用 Azure 门户基于 VHD 文件创建自定义映像：

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 选择“更多服务”，并从列表中选择“开发测试实验室”。

1. 从实验室列表，选择所需的实验室。  

1. 在实验室的边栏选项卡，选择“配置”。 

1. 在实验室的“配置”边栏选项卡上，选择“自定义映像(VHD)”。

1. 在“自定义映像”边栏选项卡上，选择“+ 添加”。

    ![添加自定义映像](./media/devtest-lab-create-template/add-custom-image.png)

1. 输入自定义映像的名称。 在创建 VM 时，此名称显示在基础映像的列表中。

1. 键入自定义映像的说明。 说明显示在基础映像的列表中。

1. 选择“VHD”。

1. 从“VHD”边栏选项卡，选择所需的 VHD 文件。

1. 选择“确定”，关闭“VHD”边栏选项卡。

1. 选择“OS 配置”。

1. 在“OS 配置”选项卡上，选择“Windows”或“Linux”。

1. 如果选择 **Windows**，请通过该复选框指定 *Sysprep* 是否已在改计算机上运行。 

1. 选择“确定”关闭“OS 配置”边栏选项卡。

1. 选择“确定”以创建自定义映像。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相关的博客文章

- [自定义映像或公式吗？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [复制 Azure 开发测试实验室间的自定义映像](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>后续步骤

- [将 VM 添加到实验室](./devtest-lab-add-vm.md)

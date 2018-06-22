---
title: 基于 VM 创建 Azure 开发测试实验室自定义映像 | Microsoft Docs
description: 介绍如何通过 Azure 门户在 Azure 开发测试实验室中基于预配的 VM 创建自定义映像
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 22f1579b2df2acdc736ed4c1d5cee64d096c320a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635910"
---
# <a name="create-a-custom-image-from-a-vm"></a>从 VM 创建自定义映像

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>分步说明

可基于预配的 VM 创建自定义映像，随后使用该自定义映像创建相同的 VM。 以下步骤演示了如何从 VM 创建自定义映像：

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 选择“所有服务”，并从列表中选择“开发测试实验室”。

1. 从实验室列表，选择所需的实验室。  

1. 在实验室的主窗格中，选择“我的虚拟机”。
 
1. 在“我的虚拟机”窗格中，选择想要从中创建自定义映像的 VM。

1. 在 VM 的管理窗格中，选择“创建自定义映像(VHD)”。

    ![创建自定义映像菜单项](./media/devtest-lab-create-template/create-custom-image.png)

1. 在“自定义映像”窗格中，输入自定义映像的名称和说明。 创建 VM 时，此信息显示在基项列表中。 自定义映像将包含 OS 磁盘和所有附加到虚拟机的数据磁盘。

    ![“创建自定义映像”窗格](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. 选择是否在 VM 上运行 sysprep。 如果未在 VM 上运行 sysprep，指定从该自定义映像创建 VM 时是否希望运行 sysprep。

1. 创建自定义映像后，选择“确定”。

几分钟之后，将创建自定义映像并将其存储在实验室的存储帐户中。 当实验室用户想要创建新 VM 时，可在基础映像的列表中找到该映像。

![自定义映像可在基础映像的列表中找到](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相关的博客文章

- [自定义映像或公式吗？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [复制 Azure 开发测试实验室间的自定义映像](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>后续步骤

- [将 VM 添加到实验室](devtest-lab-add-vm.md)

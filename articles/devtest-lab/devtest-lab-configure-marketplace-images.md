---
title: "在 Azure 开发测试实验室中配置 Azure 应用商店映像设置 |Microsoft Docs"
description: "在 Azure 开发测试实验室中创建 VM 时，配置可以使用哪个 Azure 应用商店映像"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: 5f888c9d92a9164cc7d3d1aed66c29a724b365d7
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中配置 Azure 应用商店映像设置
开发测试实验室支持基于 Azure 应用商店映像创建 VM，具体取决于配置的 Azure 应用商店映像在实验室中的使用方式。 本文演示如何指定在实验室中创建 VM 时可使用哪些 Azure 应用商店映像（如果有的话）。 这可确保团队仅有权访问所需的应用商店映像。 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>选择允许使用的 Azure 应用商店映像（创建 VM 时）
1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，并从列表中选择“开发测试实验室”。
3. 从实验室列表中，选择所需的实验室。 
4. 在实验室的边栏选项卡中，选择“配置和策略”。
5. 在实验室的“配置和策略”边栏选项卡上，选择“虚拟机基础映像”下的“Marketplace 映像”。
6. 指定是否希望所有限定的 Azure 应用商店映像可用作新 VM 的基础。 如果选择“是”，则在实验室中允许符合以下所有条件的所有 Azure 应用商店映像：
   
   * 由映像创建单个 VM，**和**
   * 映像使用 Azure Resource Manager 来预配 VM，**和**
   * 映像不需要购买额外的许可计划
     
    如果想要允许任何映像，或者想要指定可以使用的图像，则选择“否”。
     
     ![此选项可允许所有应用商店映像用作 VM 的基础映像](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. 如果选择“否”进入上一步，将启用“允许的映像/全选”复选框。 
   可使用此选项以及搜索框快速选择或取消选择显示在列表中的所有项。
   * 选中每个映像对应的复选框，分别选择允许用于创建 VM 的 Azure Marketplace 映像。
   * 如果不希望允许在实验室中使用任何 Azure 应用商店映像，请勿从列表中选择任何项。
   
    ![可指定哪个 Azure 应用商店映像可以用作 VM 的基础映像](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
配置创建 VM 时允许使用的 Azure 应用商店映像后，下一步便是[将 VM 添加到实验室](devtest-lab-add-vm-with-artifacts.md)。


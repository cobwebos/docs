---
title: "管理 Azure 开发测试实验室中的 VM 项目 | Microsoft 文档"
description: "了解如何管理 Azure 开发测试实验室中的 VM 项目"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 576509ce-6a33-4c26-87c7-de8b40271efa
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2d298c210fa46cc4473fc7e8809e55bfc102481d
ms.openlocfilehash: c970bca8f6ab9e97741370f74efc5ee66e592468
ms.lasthandoff: 02/27/2017


---
# <a name="manage-vm-artifacts-in-azure-devtest-labs"></a>管理 Azure 开发测试实验室中的 VM 项目
使用 Azure 开发测试实验室*项目*可以指定预配 VM 时执行的*操作*。 

项目操作可执行某些过程，例如运行 Windows PowerShell 脚本、运行 Bash 命令和安装软件。 

使用项目参数可为特定方案自定义项目。

本文展示了如何在实验室中管理 VM 的项目。

## <a name="add-an-existing-artifact-to-a-vm"></a>将现有项目添加到 VM
创建 VM 时，可添加现有项目。 每个实验室包括公共开发测试实验室项目存储库中的项目和已经创建并添加到自己的项目存储库的项目。
若要了解如何创建项目，请参阅文章[了解如何创建自己的项目以用于开发测试实验室](devtest-lab-artifact-author.md)。

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
1. 从实验室列表中，选择你要处理的 VM 所在的实验室。  
1. 选择“我的虚拟机”。
1. 选择所需的 VM。
1. 选择“项目”。 
1. 选择“应用项目”。
1. 在“应用项目”边栏选项卡上，选择要添加到 VM 的项目。
1. 在“添加项目”边栏选项卡上，输入所需参数值及所需的任何可选参数。  
1. 选择“添加”来添加该项目，然后返回到“应用项目”边栏选项卡。
1. 根据需要继续为 VM 添加项目。
1. 添加项目后，可以[更改项目的运行顺序](#change-the-order-in-which-artifacts-are-run)。 还可以返回到[查看或修改项目](#view-or-modify-an-artifact)。
1. 完成项目添加后，选择“应用”

## <a name="change-the-order-in-which-artifacts-are-run"></a>更改项目的运行顺序
默认情况下，按添加到 VM 的顺序执行项目操作。 以下步骤说明如何更改项目的运行顺序。

1. 在“应用项目”边栏选项卡顶部，选择指示已添加到 VM 的项目数的链接。
   
    ![添加到 VM 的项目数](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在“已选项目”边栏选项卡上，将项目拖放为所需的顺序。 **注意：**如果在拖动项目时遇到麻烦，请确保是从项目的左侧拖动。 
1. 完成后选择“确定”。  

## <a name="view-or-modify-an-artifact"></a>查看或修改项目
以下步骤说明如何查看或修改项目参数：

1. 在“应用项目”边栏选项卡顶部，选择指示已添加到 VM 的项目数的链接。
   
    ![添加到 VM 的项目数](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在“已选项目”边栏选项卡上，选择要查看或编辑的项目。  
1. 在“添加项目”边栏选项卡上，根据需要进行更改，然后选择“确定”关闭“添加项目”边栏选项卡。
1. 选择“确定”关闭“已选项目”边栏选项卡。

## <a name="save-azure-resource-manager-template"></a>保存 Azure Resource Manager 模板
Azure Resource Manager 模板提供一种声明性方式来定义可重复部署。 下列步骤说明如何为正在创建的 VM保存 Azure Resource Manager 模板。
保存后，可以使用 Azure Resource Manager 模板[通过 Azure PowerShell 部署新的 VM](../azure-resource-manager/resource-group-overview.md#template-deployment)。

1. 在“虚拟机”边栏选项卡上选择“查看 ARM 模板”。
2. 在“查看 Azure Resource Manager 模板”边栏选项卡上选择模板文本。
3. 将所选文本复制到剪贴板。
4. 选择“确定”关闭“查看 Azure Resource Manager 模板”边栏选项卡。
5. 打开文本编辑器。
6. 从剪贴板粘贴到模板文本中。
7. 保存该文件以供将来使用。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
* 了解如何[为开发测试实验室 VM 创建自定义项目](devtest-lab-artifact-author.md)。


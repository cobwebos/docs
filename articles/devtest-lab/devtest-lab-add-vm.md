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
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: de09554a0ae6ef733cc1ed6cdc20dcee014c0680
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017


---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>向 Azure 开发测试实验室中的实验室添加 VM
如果已[创建第一个 VM](devtest-lab-create-first-vm.md)，则很可能从预加载的[应用商店映像](devtest-lab-configure-marketplace-images.md)执行此操作。 现在，如果要将后续 VM 添加到实验室，还可以选择作为[自定义映像](devtest-lab-create-template.md)或[公式](devtest-lab-manage-formulas.md)的*基本*映像。 本教程将引导你完成使用 Azure 门户向开发测试实验室中的实验室添加 VM 的过程。

本文还演示了如何在实验室中管理 VM 的项目。

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
    **注意：**如果对开发测试实验室或配置项目不熟悉，请参阅[向 VM 中添加现有项目](#add-an-existing-artifact-to-a-vm)部分，然后在完成后返回此处。
1. 选择“高级设置”来配置 VM 的网络选项和到期选项。 

   若要设置过期选项，请选择“日历”图标，指定一个自动删除 VM 的日期。  默认情况下，VM 永不过期。 
1. 如果想要查看或复制 Azure Resource Manager 模板，请参阅[保存 Azure Resource Manager 模板](#save-azure-resource-manager-template)部分，然后在完成后返回此处。
1. 选择“创建”将指定的 VM 添加到实验室。
1. 实验室边栏选项卡显示 VM 的创建状态 - 先是显示为“正在创建”，然后当启动 VM 后显示为“正在运行”。

> [!NOTE]
> [添加可声明的 VM](devtest-lab-add-claimable-vm.md) 演示了如何使 VM 可声明以使其供实验室中的任意用户使用。
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>将现有项目添加到 VM
创建 VM 时，可添加现有项目。 每个实验室包括公共开发测试实验室项目存储库中的项目和已经创建并添加到自己的项目存储库的项目。

* Azure DevTest 实验室*项目*让你指定预配 VM 时执行的*操作*，如运行 Windows PowerShell 脚本、运行 Bash 命令和安装软件。
* 使用项目*参数*可为特定方案自定义项目

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

### <a name="next-steps"></a>后续步骤
* 创建 VM 后，可通过选择 VM 边栏选项卡上的“连接”来连接 VM。
* 了解如何[为开发测试实验室 VM 创建自定义项目](devtest-lab-artifact-author.md)。
* 浏览[开发测试实验室 Azure Resource Manager 快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)。


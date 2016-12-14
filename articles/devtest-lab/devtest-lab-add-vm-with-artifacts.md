---
title: "使用项目将 VM 添加到 Azure 开发测试实验室中的某个实验室 | Microsoft Docs"
description: "了解如何在 Azure 开发测试实验室中使用项目添加 VM"
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
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f480b8155c7bee797f1fed0f80200eec500e95a2
ms.openlocfilehash: aa6c87ef6e1f87cb47f68f9480ea068aa6262bf9


---
# <a name="add-a-vm-with-artifacts-to-a-lab-in-azure-devtest-labs"></a>使用项目将 VM 添加到 Azure 开发测试实验室中的某个实验室
> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/How-to-create-VMs-with-Artifacts-in-a-DevTest-Lab/player]
> 
> 

通过基本映像在实验室中创建 VM，该基本映像是[自定义映像](devtest-lab-create-template.md)[公式](devtest-lab-manage-formulas.md) 或 [应用商店映像](devtest-lab-configure-marketplace-images.md)。

使用开发测试实验室项目可指定创建 VM 时执行的操作。 

项目操作可执行某些过程，例如运行 Windows PowerShell 脚本、运行 Bash 命令和安装软件。 

使用项目参数可为特定方案自定义项目。

本文章演示如何使用项目在实验室中创建 VM。

## <a name="add-a-vm-with-artifacts"></a>使用项目添加 VM
1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
3. 从实验室列表中选择要在其中创建 VM 的实验室。  
4. 在实验室的“概述”边栏选项卡上，选择“+ 虚拟机”。  
    ![添加 VM 按钮](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)
5. 在“选择基本映像”边栏选项卡上为 VM 选择基本映像c。
6. 在“虚拟机”边栏选项卡的“虚拟机名称”文本框中输入新虚拟机的名称。
   
    ![实验室 VM 边栏选项卡](./media/devtest-lab-add-vm-with-artifacts/devtestlab-lab-vm-blade.png)
7. 输入“用户名”，它将被授予虚拟机上的管理员权限。  
8. 如果想要使用在“密码存储”中存储的密码，请选择“使用密码存储中的密码”，然后指定与密码对应的密钥值。 或者，在标记为“键入值”的文本字段输入一个密码。
9. 选择“虚拟机大小”，并选择指定待创建 VM 的处理器内核、RAM 大小和硬盘驱动器大小的预定义项之一。
10. 选择“虚拟网络”，然后选择所需的虚拟网络。
11. 选择“子网”，然后选择子网。
12. 如果实验室策略设置为允许所选子网的公共 IP 地址，请通过选择“是”或“否”来指定是否要公开此IP地址。 否则，将禁用此选项并选择为“否”。 
13. 选择“项目”，然后从项目列表中选择并配置要添加到基础映像的项目。 
    **注意：**如果对开发测试实验室或配置项目不熟悉，请跳到[向 VM 中添加现有项目](#add-an-existing-artifact-to-a-vm)部分，然后在完成后返回此处。
14. 如果想要查看或复制 Azure Resource Manager 模板，请跳到[保存 Azure Resource Manager 模板](#save-azure-resource-manager-template)部分，然后在完成后返回此处。
15. 选择“创建”将指定的 VM 添加到实验室。
16. 实验室边栏选项卡显示 VM 的创建状态；首先显示为“创建”，然后当启动 VM 后显示为“运行”。
17. 转到[接下来的步骤](#next-steps)部分。 

## <a name="add-an-existing-artifact-to-a-vm"></a>将现有项目添加到 VM
创建 VM 时，可添加现有项目。 每个实验室包括公共开发测试实验室项目存储库中的项目和已经创建并添加到自己的项目存储库的项目。
若要了解如何创建项目，请参阅文章[了解如何创建自己的项目以用于开发测试实验室](devtest-lab-artifact-author.md)。

1. 在“虚拟机”边栏选项卡上选择“项目”。 
2. 在“添加项目”边栏选项卡上选择所需项目。  
   
    ![添加项目边栏选项卡](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)
3. 输入所需参数值及所需的任何可选参数。  
4. 选择“添加”来添加该项目，然后返回到“添加项目”边栏选项卡。
5. 根据需要继续为 VM 添加项目。
6. 添加项目后，可以[更改项目的运行顺序](#change-the-order-in-which-artifacts-are-run)。 还可以返回到[查看或修改项目](#view-or-modify-an-artifact)。

## <a name="change-the-order-in-which-artifacts-are-run"></a>更改项目的运行顺序
默认情况下，按添加到 VM 的顺序执行项目操作。 以下步骤说明如何更改项目的运行顺序。

1. 在“添加项目” 边栏选项卡顶部，选择指示已添加到 VM 的项目数的链接。
   
    ![添加到 VM 的项目数](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
2. 若要指定项目的运行顺序，请按所需顺序拖放项目。 **注意：**如果在拖动项目时遇到问题，请确保是从项目的左侧拖动。 
3. 完成后选择“确定”。  

## <a name="view-or-modify-an-artifact"></a>查看或修改项目
以下步骤说明如何查看或修改项目参数：

1. 在“添加项目” 边栏选项卡顶部，选择指示已添加到 VM 的项目数的链接。
   
    ![添加到 VM 的项目数](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
2. 在“已选项目”边栏选项卡上，选择想要查看或编辑的项目。  
3. 在“添加项目”边栏选项卡上，根据需要进行更改，然后选择“确定”关闭“添加项目”边栏选项卡。
4. 选择“确定”关闭“已选项目”边栏选项卡。

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
* 创建 VM 后，可通过选择 VM 边栏选项卡上的“连接”来连接 VM。
* 了解如何[为开发测试实验室 VM 创建自定义项目](devtest-lab-artifact-author.md)。
* 浏览[开发测试实验室 ARM 快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)




<!--HONumber=Nov16_HO3-->



---
title: 向 Azure 开发测试实验室中的实验室添加 VM | Microsoft 文档
description: 了解如何向 Azure 开发测试实验室中的实验室添加虚拟机
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 22060cc6dd5eb15e81a0c397a7b0255f16780d74
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976494"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>向 Azure 开发测试实验室中的实验室添加 VM
如果已[创建第一个 VM](tutorial-create-custom-lab.md#add-a-vm-to-the-lab)，则很可能从预加载的[市场映像](devtest-lab-configure-marketplace-images.md)执行此操作。 现在，如果要将后续 VM 添加到实验室，还可以选择作为[自定义映像](devtest-lab-create-template.md)或[公式](devtest-lab-manage-formulas.md)的*基本*映像。 本教程会引导完成使用 Azure 门户向开发测试实验室中的实验室添加 VM 的过程。

本文还演示了如何在实验室中管理 VM 的项目。

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>向 Azure 开发测试实验室中的实验室添加 VM 的步骤
1. 登录 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 选择“所有服务”，然后在“DEVOPS”部分中选择“开发测试实验室”。 如果在“DEVOPS”部分中选择了“开发测试实验室”旁边的 *（星号）， 此操作会将“开发测试实验室”添加到左侧导航菜单中，以便你下次可以轻松访问它。 然后，可以在左侧导航菜单中选择“开发测试实验室”。

    ![所有服务 - 选择“开发测试实验室”](./media/devtest-lab-create-lab/all-services-select.png)
1. 从实验室列表中选择要在其中创建 VM 的实验室。
2. 在实验室的“概述”页上，选择“+ 添加”。

    ![“添加 VM”按钮](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. 在“选择基础映像”页面上，为 VM 选择一个市场映像。
1. 在“虚拟机”页面的“基本设置”选项卡上，执行以下操作：
    1. 在“虚拟机名称”文本框中，输入 VM 的名称。 该文本框会使用唯一的自动生成名称进行预填充。 该名称对应于电子邮件地址中的用户名，后接一个唯一的 3 位数字。 此功能可节省每次创建计算机时考虑计算机名称和键入该名称的时间。 如果愿意，可以用自己选择的名称替代此自动填充的字段。 若要替代 VM 的自动填充名称，请在“虚拟机名称”文本框中输入名称。
    2. 输入在虚拟机上被授予了管理员权限的**用户名**。 使用唯一的自动生成名称预先填充计算机的“用户名”。 该名称对应于电子邮件地址中的用户名。 此功能可节省每次创建新计算机时确定用户名的时间。 同样，如果愿意，可以用自己选择的用户名替代此自动填充的字段。 若要替代用户名的自动填充值，请在“用户名”文本框中输入值。 此用户在虚拟机上被授予了管理员权限。
    3. 如果要在实验室中创建第一个 VM，请输入用户的密码。 若要将此密码保存为与实验室关联的 Azure Key Vault 中的默认密码，请选择“另存为默认密码”。 默认密码保存在名为“VmPassword”的密钥保管库中。 尝试在实验室中创建后续 VM 时，会自动选择“VmPassword”作为密码。 若要替代该值，请清除“使用已保存的机密”复选框，然后输入密码。

        ![选择一个库](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        还可以先在密钥保管库中保存机密，然后在实验室中创建 VM 时使用它。 有关详细信息，请参阅[在密钥保管库中存储机密](devtest-lab-store-secrets-in-key-vault.md)。 若要使用在密钥保管库中存储的密码，请选择“使用已保存的机密”，并指定与机密（密码）对应的密钥值。
    4. 在“更多选项”部分中，选择“更改大小”。 选择指定了要创建的 VM 的处理器内核、RAM 大小和硬盘驱动器大小的预定义项之一。
    5. 选择“添加或删除项目”。 选择并配置要添加到基础映像的项目。
    **注意：** 如果对开发测试实验室或配置项目不熟悉，请参阅[向 VM 中添加现有项目](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)部分，并在完成后返回此处。
2. 切换到顶部的“高级设置”选项卡，然后执行以下操作：
    1. 若要更改 VM 所在的虚拟网络，请选择“更改 VNet”。
    2. 若要更改子网，请选择“更改子网”。
    3. 指定 VM 的 IP 地址是**公用、专用还是共享**。
    4. 若要自动删除 VM，请指定“到期日期和时间”。
    5. 要使 VM 可由实验室用户认领，对于“使此虚拟机可认领”选项，请选择“是”。
    6. 指定想要使其可供实验室用户使用的 **VM 实例**数量。

        ![选择一个库](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. 选择“创建”将指定的 VM 添加到实验室。

   实验室页面将显示 VM 的创建状态 - 先是显示为“正在创建”，然后在 VM 启动后显示为“正在运行”。

    ![VM 创建状态](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>将现有项目添加到 VM
创建 VM 时，可添加现有项目。 每个实验室包括公共开发测试实验室项目存储库中的项目和已经创建并添加到自己的项目存储库的项目。

* Azure 开发测试实验室项目可让你指定预配 VM 时执行的操作，如运行 Windows PowerShell 脚本、运行 Bash 命令和安装软件。
* 使用项目*参数*可为特定方案自定义项目

若要了解如何创建项目，请参阅文章[了解如何创建自己的项目以用于开发测试实验室](devtest-lab-artifact-author.md)。

1. 登录 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 选择“所有服务”，并从列表中选择“开发测试实验室”。
1. 从实验室列表中，选择要处理的 VM 所在的实验室。
1. 选择“我的虚拟机”。
1. 选择所需的 VM。
1. 选择“管理项目”。
1. 选择“应用项目”。
1. 在“应用项目”窗格上，选择要添加到 VM 的项目。
1. 在“添加项目”窗格上，输入所需参数值及所需的任何可选参数。
1. 选择“添加”以添加该项目，并返回到“应用项目”窗格。
1. 根据需要继续为 VM 添加项目。
1. 添加项目后，可以[更改项目的运行顺序](#change-the-order-in-which-artifacts-are-run)。 还可以返回到[查看或修改项目](#view-or-modify-an-artifact)。
1. 完成项目添加后，选择“应用”

## <a name="change-the-order-in-which-artifacts-are-run"></a>更改项目的运行顺序
默认情况下，按添加到 VM 的顺序执行项目操作。
以下步骤说明如何更改项目的运行顺序。

1. 在“应用项目”窗格顶部，选择指示已添加到 VM 的项目数的链接。

    ![添加到 VM 的项目数](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在“已选项目”窗格上，将项目拖放为所需的顺序。 **注意：** 如果在拖动项目时遇到麻烦，请确保是从项目的左侧拖动。
1. 完成后选择“确定”。

## <a name="view-or-modify-an-artifact"></a>查看或修改项目
以下步骤说明如何查看或修改项目参数：

1. 在“应用项目”窗格顶部，选择指示已添加到 VM 的项目数的链接。

    ![添加到 VM 的项目数](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在“已选项目”窗格上，选择要查看或编辑的项目。
1. 在“添加项目”窗格上，根据需要进行更改，并选择“确定”关闭“添加项目”窗格。
1. 选择“确定”关闭“已选项目”窗格。

## <a name="save-azure-resource-manager-template"></a>保存 Azure 资源管理器模板
Azure 资源管理器模板提供一种声明性方式来定义可重复部署。
下列步骤说明如何为正在创建的 VM保存 Azure 资源管理器模板。
保存后，可以通过 Azure PowerShell 使用 Azure 资源管理器[部署新的 VM](../azure-resource-manager/templates/overview.md)。

1. 在“虚拟机”窗格上，选择“查看 Azure 资源管理器模板”。
2. 在“查看 Azure 资源管理器模板”窗格上选择模板文本。
3. 将所选文本复制到剪贴板。
4. 选择“确定”关闭“查看 Azure 资源管理器模板”窗格。
5. 打开文本编辑器。
6. 从剪贴板粘贴到模板文本中。
7. 保存该文件以供将来使用。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
* 创建 VM 后，可通过选择 VM 窗格上的“连接”来连接 VM。
* 了解如何[为开发测试实验室 VM 创建自定义项目](devtest-lab-artifact-author.md)。
* 浏览[开发测试实验室 Azure 资源管理器快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)。

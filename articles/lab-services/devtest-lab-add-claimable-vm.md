---
title: 在 Azure 开发测试实验室中创建和管理可认领 Vm |Microsoft Docs
description: 了解如何向 Azure 开发测试实验室中的实验室添加可声明的虚拟机
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 13d642597fdf5d0eae6c6fd4f0cab16181f033c2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390961"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建和管理可认领 VM
使用与[添加标准 VM](devtest-lab-add-vm.md) 类似的方式向实验室添加可认领 VM：通过作为[自定义映像](devtest-lab-create-template.md)、[公式](devtest-lab-manage-formulas.md)或[市场映像](devtest-lab-configure-marketplace-images.md)的“基”添加。 本教程指导你使用 Azure 门户向开发测试实验室中的实验室添加可认领 VM，并展示了认领和取消认领 VM 时需遵循的过程。

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>向 Azure 开发测试实验室中的实验室添加可认领 VM 的步骤
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
    3. 如果要在实验室中创建第一个 VM，请输入用户的密码。 若要将此密码保存为与实验室关联的 Azure Key Vault 中的默认密码，请选择“另存为默认密码”。 默认密码保存在名为**VmPassword** 的密钥保管库中。 尝试在实验室中创建后续 VM 时，会自动选择“VmPassword”作为密码。 若要替代该值，请清除“使用已保存的机密”复选框，然后输入密码。

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
3. 选择“创建”将指定的 VM 添加到实验室。

   实验室页面将显示 VM 的创建状态 - 先是显示为“正在创建”，然后在 VM 启动后显示为“正在运行”。

> [!NOTE]
> 如果通过  [Azure 资源管理器模板](devtest-lab-create-environment-from-arm.md)部署实验室 VM，则可通过将属性部分中的 **allowClaim** 属性设置为 true 来创建可声明的 VM。


## <a name="using-a-claimable-vm"></a>使用可认领 VM

用户可执行以下某步骤声明“可认领的虚拟机”列表中的任何 VM：

* 从实验室的“概述”页面底部的“可认领虚拟机”列表中，右键单击列表中的某个 VM，并选择“认领虚拟机”。

  ![请求可认领的特定 VM。](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* 在“概述”页面顶部，选择“认领任意虚拟机”。 从“可认领 VM”列表中分配随机的虚拟机。

  ![请求任意可认领 VM。](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


用户声明 VM 后，开发测试实验室将启动该计算机，并将其移动到实验室用户的 "我的虚拟机" 列表中。 这意味着，实验室用户现在将拥有此计算机的所有者权限。 此步骤所需的时间可能不同，具体取决于启动时间以及在声明事件期间执行的任何其他自定义操作。 一旦声明后，计算机将不再在可认领池中可用。  

## <a name="unclaim-a-vm"></a>取消认领 VM

如果用户完成了对认领的 VM 的使用并且希望使其可供他人使用，则可以通过执行下列步骤之一将认领的 VM 返还到可认领虚拟机的列表中：

- 从“我的虚拟机”列表中，右键单击列表中的一个虚拟机或选择其省略号（...），然后选择“取消认领”。

  ![在 VM 列表中取消认领 VM。](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- 在“我的虚拟机”列表中，选择一个 VM 以打开其管理窗格，然后从顶部菜单栏中选择“取消认领”。

  ![在 VM 的管理窗格中取消认领 VM。](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

当用户取消认领某个 VM 时，他们将不再拥有该特定实验室 VM 的所有者权限，并且该虚拟机可供任何其他实验室用户使用 retured 到该池的状态。 

### <a name="transferring-the-data-disk"></a>转移数据磁盘
如果某个可认领 VM 具有附加到它的数据磁盘，并且用户取消认领了该 VM，则数据磁盘仍然跟随该 VM。 然后，当另一用户认领该 VM 时，该新用户同时认领了该数据磁盘和 VM。

这称为“转移数据磁盘”。 然后，该数据磁盘将出现在新用户的“我的数据磁盘”列表中，以便进行管理。

![取消认领数据磁盘。](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>后续步骤
* 在创建后，可以通过在 VM 的管理窗格上选择“连接”来连接到 VM。
* 探究[开发测试实验室 Azure 资源管理器快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)。

---
title: 快速入门 - 在 Azure 门户中启用适用于 VM 的 Azure 自动管理
description: 了解如何在 Azure 门户中对新的或现有 VM 快速启用适用于虚拟机的自动管理。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 43e0805a828a2ed32d23984b6ffef95a7e710953
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943404"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>快速入门：在 Azure 门户中启用适用于虚拟机的 Azure 自动管理

使用 Azure 门户在新的或现有虚拟机上启用自动管理，从而开始使用适用于虚拟机的 Azure 自动管理。


## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)。

> [!NOTE]
> 免费试用帐户无法访问本教程中使用的虚拟机。 请升级为即用即付订阅。

> [!IMPORTANT]
> 启用自动管理需要以下 RBAC 权限：“所有者”角色或“参与者”以及“用户访问管理员”角色  。


## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com/)。


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>在现有 VM 上启用适用于 VM 的自动管理

1. 在搜索栏中，搜索并选择“自动管理 – Azure 虚拟机最佳做法”。

2. 选择“在现有 VM 上启用”。

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="在现有 VM 上启用。":::

3. 在“选择计算机”边栏选项卡上：
    1. 按“订阅”和“资源组”筛选 VM 列表 。
    1. 选中要加入的每个虚拟机的复选框。
    1. 单击“选择”按钮。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="从可用 VM 列表中选择现有 VM。":::

4. 在“配置文件”下，单击“浏览并更改配置文件和首选项” 。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="浏览并更改配置文件和首选项。":::

5. 在“选择配置文件 + 首选项”边栏选项卡上：
    1. 在左侧选择配置文件：“开发/测试”用于测试，“生产”用于生产 。
    1. 单击“选择”按钮。

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="浏览生产配置文件。":::

6. 单击“启用”按钮****。


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>在新 VM 上启用适用于 VM 的自动管理

1. 按照[快速入门 - 在 Azure 门户中创建 Windows VM](..\virtual-machines\windows\quick-create-portal.md) 中的创建步骤进行操作。

2. 部署 VM 之后，你会登录部署状态页，该页在底部推荐了“后续步骤”。

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="位于部署页底部的“后续步骤”部分。":::

3. 在“后续步骤”下，选择“启用自动管理虚拟机最佳做法” 。

4. 在“自动管理 – Azure 虚拟机最佳做法”页上，“计算机”会自动由新创建的 VM 进行填充 。

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="新创建的 VM 会显示为所选计算机。":::

5. 在“配置文件”下，单击“浏览并更改配置文件和首选项” 。

6. 在“选择配置文件 + 首选项”边栏选项卡上：
    1. 在左侧选择配置文件：“开发/测试”用于测试，“生产”用于生产 。
    1. 单击“选择”按钮。

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="浏览生产配置文件。":::

7. 单击“启用”按钮****。


## <a name="disable-automanage-for-vms"></a>禁用适用于 VM 的自动管理

可通过禁用自动管理来快速停止适用于虚拟机的 Azure 自动管理。

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="在虚拟机上禁用自动管理。":::

1. 请转到“自动管理 – Azure 虚拟机最佳做法”页，其中列出了自动管理的所有 VM。
1. 选中要禁用的虚拟机旁的复选框。
1. 单击“禁用自动管理”按钮。
1. 在同意“禁用”之前，请仔细阅读生成的弹出窗口中的消息。


## <a name="clean-up-resources"></a>清理资源

如果创建了新资源组来尝试适用于虚拟机的 Azure 自动管理，但不再需要它，则可以删除该资源组。 删除组时也会删除资源组中包含的 VM 和所有其他资源。

Azure 自动管理会创建用于存储资源的默认资源组。 检查具有命名约定“DefaultResourceGroupRegionName”和“AzureBackupRGRegionName”的资源组，以清理所有资源。

1. 选择“资源组”。
1. 在资源组页上，选择“删除”。
1. 出现提示时，确认资源组的名称，然后选择“删除”。


## <a name="next-steps"></a>后续步骤

在本快速入门中，你启用了适用于 VM 的 Azure 自动管理。 

了解如何在对虚拟机启用自动管理时创建和应用自定义首选项。 

> [!div class="nextstepaction"]
> [适用于 VM 的 Azure 自动管理 - 定义配置文件](virtual-machines-custom-preferences.md)

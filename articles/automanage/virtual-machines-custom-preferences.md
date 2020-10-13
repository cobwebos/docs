---
title: 在 Azure Automanage 中为 Vm 创建自定义首选项
description: 了解如何在 Azure Automanage 中为 Vm 调整配置文件，并设置自己的首选项。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 377677c9e5e81487059241db68baff639a3de033
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715047"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>在 Azure Automanage 中为 Vm 创建自定义首选项

Azure Automanage for 虚拟机最佳实践包含可根据需要进行调整的默认配置文件。 本文将介绍在新的或现有的 VM 上启用 automanagement 时，如何设置自己的配置文件首选项。

目前支持 [Azure 备份](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) 和 [Microsoft 反恶意软件](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration)的自定义。


> [!NOTE]
> 启用 Automanage 时，不能在 VM 上更改配置文件或首选项。 需要为该 VM 禁用 Automanage，然后使用所需的配置配置文件和首选项重新启用 Automanage。


## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)。

> [!NOTE]
> 免费试用帐户无法访问本教程中使用的虚拟机。 请升级到即用即付订阅。

> [!IMPORTANT]
> 若要启用 Automanage： **Owner** 角色或 **参与者** 以及 **用户访问管理员** 角色，需要以下 Azure RBAC 权限。


## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com/)。


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>在现有 VM 上启用适用于 VM 的自动管理

1. 在搜索栏中，搜索并选择“自动管理 – Azure 虚拟机最佳做法”。

2. 选择“在现有 VM 上启用”。

3. 在“选择计算机”边栏选项卡上：
    1. 按“订阅”和“资源组”筛选 VM 列表 。
    1. 选中要加入的每个虚拟机的复选框。
    1. 单击“选择”按钮。

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="从可用 VM 列表中选择现有 VM。":::

4. 在“配置文件”下，单击“浏览并更改配置文件和首选项” 。

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="从可用 VM 列表中选择现有 VM。" 边栏选项卡上，选择左侧的配置文件：用于测试的 *开发/测试* *，生产* 生产。

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="从可用 VM 列表中选择现有 VM。" 选项卡：
        1. 订阅
        1. 资源组
        1. 首选项名称
        1. 区域

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="从可用 VM 列表中选择现有 VM。" 选项卡并调整所需的配置首选项。
        
    > [!NOTE]
    > 更改配置文件配置时，仅允许在最佳方案上限和下限范围内进行的调整。

8. 查看配置文件。
9. 单击“创建”  按钮。

10. 单击“启用”按钮****。


## <a name="disable-automanage-for-vms"></a>禁用适用于 VM 的自动管理

可通过禁用自动管理来快速停止适用于虚拟机的 Azure 自动管理。

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="从可用 VM 列表中选择现有 VM。":::

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

获取常见问题中解答的最常见问题。 

> [!div class="nextstepaction"]
> [常见问题解答](faq.md)
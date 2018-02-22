---
title: "对 Azure 开发测试实验室中的虚拟机执行附加或分离数据磁盘操作 | Microsoft Docs"
description: "了解如何对 Azure 开发测试实验室中的虚拟机执行附加或分离数据磁盘操作"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: v-craic
ms.openlocfilehash: 7a0a86f658ac19bfb197c7e233b8717bf9fba40b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>对 Azure 开发测试实验室中的虚拟机执行附加或分离数据磁盘操作
[Azure 托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)管理与虚拟机数据磁盘关联的存储帐户。 用户将新数据磁盘附加到 VM，指定所需的磁盘的类型和大小，然后 Azure 会自动创建并管理磁盘。 数据磁盘可以从 VM 中分离，并且可以在以后重新附加到同一 VM 或附加到属于同一用户的其他 VM。

通过此功能可以很方便地管理每个单独虚拟机外部的存储或软件。 如果存储或软件已存在于数据磁盘内部，拥有该数据磁盘的用户可以轻松地对任何 VM 执行附加、分离和重新附加该数据磁盘的操作。

## <a name="attach-a-data-disk"></a>附加数据磁盘
将数据磁盘附加到 VM 之前，请查看以下提示：

- VM 的大小决定了可以附加多少个数据磁盘。 有关详细信息，请参阅[虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。
- 只能将数据磁盘附加到正在运行的 VM。 尝试附加数据磁盘之前，请确保 VM 正在运行。

### <a name="attach-a-new-disk"></a>附加新磁盘
请按照下列步骤创建新的托管数据磁盘并将其附加到 Azure 开发测试实验室中的 VM。

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 选择“所有服务”，并从列表中选择“开发测试实验室”。
1. 从实验室列表，选择所需的实验室。 
1. 从“我的虚拟机”列表中，选择一个正在运行的虚拟机。
1. 从左侧菜单中选择“磁盘”。

    ![为虚拟机选择数据磁盘](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. 选择“附加新磁盘”以创建新的数据磁盘并将其附加到 VM。

    ![将新的数据磁盘附加到虚拟机](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. 通过输入数据磁盘名称、类型和大小，完成“附加新磁盘”窗格。

    ![完成“附加新磁盘”表单](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. 选择“确定”。

稍后，新的数据磁盘会被创建并附加到 VM，并且将出现在该 VM 的“数据磁盘”列表中。

### <a name="attach-an-existing-disk"></a>附加现有磁盘
请按照下列步骤将现有可用数据磁盘重新附加到正在运行的 VM。 

1. 选择要将数据磁盘重新附加到的正在运行的 VM。
1. 从左侧菜单中选择“磁盘”。
1. 选择“附加现有磁盘”将可用数据磁盘附加到 VM。

    ![将现有数据磁盘附加到虚拟机](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. 从“附加现有磁盘”窗格中选择“确定”。

    ![将现有数据磁盘附加到虚拟机](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

稍后，数据磁盘会附加到 VM，并且将出现在该 VM 的“数据磁盘”列表中。

## <a name="detach-a-data-disk"></a>分离数据磁盘
不再需要已附加到 VM 的数据磁盘时，可以轻松地分离它。 执行分离操作会从 VM 中删除磁盘，但会将该磁盘保留在存储中供以后使用。

如果希望再次使用该磁盘上的现有数据，可以将其重新附加到同一虚拟机或其他虚拟机。

### <a name="detach-from-the-vms-management-pane"></a>在 VM 的管理窗格中分离
1. 从虚拟机列表中，选择已附加了数据磁盘的 VM。
1. 从左侧菜单中选择“磁盘”。

    ![为虚拟机选择数据磁盘](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. 从“数据磁盘”列表中，选择要分离的数据磁盘。
1. 从磁盘的详细信息窗格顶部选择“分离”。

    ![分离数据磁盘](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. 选择“是”确认要分离该数据磁盘。

该磁盘已分离，并且可供附加到另一个 VM。 
### <a name="detach-from-the-labs-main-pane"></a>从实验室的主窗格中分离
1. 在实验室主窗格中，选择“我的数据磁盘”。

    ![访问实验室的数据磁盘](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. 右键单击要分离的数据磁盘，或者选择其省略号 (...)，然后选择“分离”。

    ![分离数据磁盘](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. 选择“是”确认要分离该数据磁盘。

   > [!NOTE]
   > 如果数据磁盘已分离，可以通过选择“删除”来选择从可用数据磁盘列表中删除该数据磁盘。
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>升级非托管数据磁盘
如果现有 VM 使用非托管磁盘，可以轻松地将该 VM 转换为使用托管磁盘。 此过程将同时转换 OS 磁盘和任何附加的数据磁盘。

若要升级非托管数据磁盘，请按照本文中概述的步骤从非托管 VM [分离数据磁盘](#detach-a-data-disk)。 然后，向托管 VM [重新附加磁盘](#attach-an-existing-disk)，以自动将该数据磁盘从非托管升级到托管。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
了解如何为[可认领虚拟机](devtest-lab-add-claimable-vm.md#unclaim-a-vm)管理数据磁盘。


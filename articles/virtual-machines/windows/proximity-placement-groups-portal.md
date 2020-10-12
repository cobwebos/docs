---
title: 使用门户创建邻近放置组
description: 了解如何使用 Azure 门户创建邻近放置组。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/24/2020
ms.author: cynthn
ms.openlocfilehash: 6a14e2bd7385430c3d0fbec06259a876af556e38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82190395"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>使用门户创建邻近放置组

若要让 VM 尽可能靠近，将延迟尽可能降至最低，应将 VM 部署到一个[邻近放置组](co-location.md#proximity-placement-groups)中。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源在物理上彼此靠近。 邻近放置组用于要求低延迟的工作负荷。

> [!NOTE]
> 邻近放置组不能与专用主机一起使用。
>
> 如果要将可用性区域与放置组一起使用，则需要确保放置组中的 Vm 也全部位于同一可用性区域中。
>

## <a name="create-the-proximity-placement-group"></a>创建邻近放置组

1. 在搜索中键入“邻近放置组”。
1. 在搜索结果中的“服务”下，选择“邻近放置组”。
1. 在“邻近放置组”页面中，选择“添加”。
1. 在“基本信息”选项卡中的“项目详细信息”下，确保选择了正确的订阅。
1. 在“资源组”中选择“新建”来创建新的资源组，或者从下拉列表中选择已存在的空资源组。 
1. 在“区域”中，选择要在其中创建邻近放置组的位置。
1. 在“邻近放置组名称”中键入一个名称，然后选择“查看 + 创建”。
1. 在通过验证后，选择“创建”以创建邻近放置组。

    ![创建邻近放置组的屏幕截图](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>创建 VM

1. 在门户中创建 VM 时，转到“高级”选项卡。 
1. 在“邻近放置组”部分，选择正确的邻近放置组。 

    ![在门户中新建 VM 时“邻近放置组”部分的屏幕截图](./media/ppg/vm-ppg.png)

1. 在完成所有其他必需选择后，选择“查看 + 创建”。
1. 通过验证后，选择“创建”，在邻近放置组中部署 VM。


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>将现有可用性集中的 VM 添加到到邻近放置组中

如果虚拟机是可用性集的一部分，则需要先将可用性集添加到放置组，然后再添加 VM。

1. 在[门户](https://portal.azure.com)中搜索“可用性集”并从结果中选择可用性集。
1. 选择 VM，并在 VM 所对应的页面上选择“停止”，然后选择“确定”来停止/解除分配可用性集中的每个 VM 。
1. 在可用性集的页面上，确保所有 VM 的“状态”列为“已停止(已解除分配)” 。
1. 在左侧菜单中选择“配置”。
1. 在“邻近放置组”下，从下拉列表选择一个放置组，然后选择“保存” 。
1. 从左侧菜单中选择“概述”以再次查看 VM 列表。 
1. 选择可用性集中的每个 VM，然后在每个 VM 的页面上选择“启动”。 


## <a name="add-existing-vm-to-placement-group"></a>将现有 VM 添加到放置组 


1. 在 VM 的页面上，选择“停止”。
1. 将 VM 的状态列为“已停止(已解除分配)”后，在左侧菜单中选择“配置” 。
1. 在“邻近放置组”下，从下拉列表选择一个放置组，然后选择“保存” 。
1. 在左侧菜单中选择“概述”，然后选择“启动”以重启 VM 。

 

## <a name="next-steps"></a>后续步骤

也可使用 [Azure PowerShell](proximity-placement-groups.md) 创建邻近放置组。


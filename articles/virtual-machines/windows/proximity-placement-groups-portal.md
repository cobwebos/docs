---
title: 使用门户创建邻近布局组
description: 了解如何使用 Azure 门户创建邻近位置组。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: aaecfbd14289840e795c6323737877e267586e16
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098639"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>使用门户创建邻近布局组

若要尽可能降低 Vm，请尽可能降低延迟，然后将它们部署到[邻近的放置组](co-location.md#proximity-placement-groups)。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源在物理上彼此靠近。 邻近放置组用于要求低延迟的工作负荷。


## <a name="create-the-proximity-placement-group"></a>创建邻近位置组

1. 在搜索中键入**邻近位置组**。
1. 在搜索结果中的 "**服务**" 下，选择**邻近位置组**。
1. 在 "**邻近性放置组**" 页中，选择 "**添加**"。
1. 在“基本信息”**** 选项卡中的“项目详细信息”**** 下，确保选择了正确的订阅。
1. 在 "**资源组**" 中，选择 "**新建**" 以创建新组，或从下拉端选择现有的资源组。
1. 在 "**区域**" 中，选择要在其中创建邻近位置组的位置。
1. 在 "**邻近性放置组名称**" 中键入一个名称，然后选择 "**查看 + 创建**"。
1. 验证通过后，选择 "**创建**" 以创建邻近感应放置组。

    ![创建邻近位置组的屏幕截图](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>创建 VM

1. 在门户中创建 VM 时，请使用 "**高级**" 选项卡。 
1. 在 "**邻近性放置组**" 选项中，选择正确的放置组。 

    ![在门户中创建新 VM 时的邻近位置组部分的屏幕截图](./media/ppg/vm-ppg.png)

1. 完成所需的所有其他选择后，请选择 "**查看 + 创建**"。
1. 通过验证后，选择 "**创建**" 将 VM 部署到放置组。




## <a name="next-steps"></a>后续步骤

你还可以使用[Azure PowerShell](proximity-placement-groups.md)来创建邻近位置组。


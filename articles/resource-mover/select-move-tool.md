---
title: 选择用于跨区域移动 Azure 资源的工具
description: 查看用于跨区域移动 Azure 资源的选项和工具
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1b233028b52175842c73660ff116ac592d8296e0
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603350"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>选择用于移动 Azure 资源的工具

你可以在 Azure 中移动资源，如下所述：

- **跨区域移动资源**：可以在资源转移器中心移动资源，也可以在资源组内进行移动。 
- **跨资源组/订阅移动资源**：在资源组内进行移动。 
- **在 Azure 云之间移动资源**：使用 Azure Site Recovery 服务在公有云和政府云之间移动资源。
- **在同一区域中的可用性区域之间移动资源**：使用 Azure Site Recovery 服务在同一 Azure 区域中的可用性区域之间移动资源。


## <a name="compare-move-tools"></a>比较移动工具

**工具** | **使用时机** | **了解详细信息** 
--- | --- | ---
**在资源组内进行移动** | 将资源移动到不同的资源组/订阅或跨区域移动。<br/><br/> 如果跨区域移动，请在资源组中选择要移动的资源，然后转到资源转移器中心，以便验证依赖项并将资源移动到目标区域。 | [将资源移动到另一个资源组/订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。<br/><br/> [将资源从资源组移动到其他区域](move-region-within-resource-group.md)。
**从资源转移器中心进行移动** | 跨区域移动资源。 <br/><br/> 你可以移动到目标区域，或移动到目标区域中的特定可用性区域或可用性集。 | [在资源转移器中心跨区域移动资源]()。
**使用 Site Recovery 移动 VM** | 用于在政府云和公有云之间移动 Azure VM。<br/><br/> 如果要在同一区域中的可用性区域之间移动 VM，请使用此工具。 |[在政府云/公有云之间移动资源](../site-recovery/region-move-cross-geos.md)，[将资源移动到同一区域中的可用性区域](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)。

## <a name="next-steps"></a>后续步骤

[详细了解](about-move-process.md)资源转移器组件和移动过程。

---
title: 管理在 Azure 资源移动器中的 VM 移动过程中创建的资源
description: 了解如何管理 Azure 资源移动器中 VM 移动过程中创建的资源
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 4ff1905eb164d004af69ce5b0df3278bf3a46884
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670289"
---
# <a name="manage-resources-created-for-the-vm-move"></a>管理为 VM 移动创建的资源

本文介绍如何管理 [Azure 资源移动器](overview.md) 明确创建的资源，以便加速 VM 移动过程。 

跨区域移动 Vm 后，资源移动器会创建多个资源，这些资源应手动清除。

## <a name="delete-resources-created-for-vm-move"></a>删除为 VM 移动创建的资源

手动删除移动集合，并 Site Recovery 为 VM 移动创建的资源。

1. 在移动了 Vm 的源区域中，查看资源组中的资源 ```RegionMoveRG-<sourceregion>-<target-region>``` 。
2. 检查移动集合中的 VM 和所有其他源资源是否已移动/删除。 这可确保没有任何挂起的资源使用它们。
2. 删除这些资源。

    - 移动集合的名称为 ```movecollection-<sourceregion>-<target-region>``` 。
    - 缓存存储帐户名称为 ```resmovecache<guid>```
    - 保管库名称是 ```ResourceMove-<sourceregion>-<target-region>-GUID``` 。

## <a name="next-steps"></a>后续步骤

尝试使用资源移动器将 [VM 移](tutorial-move-region-virtual-machines.md) 到另一个区域。
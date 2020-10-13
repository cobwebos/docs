---
title: 使用 Azure Site Recovery 在灾难恢复到 Azure 时排除 Hyper-V VM 磁盘
description: 如何使用 Azure Site Recovery 在复制到 Azure 时排除 Hyper-V VM 磁盘。
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 373cffe96119af4a2fc0d74e2090e6cc24dcaf8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86131256"
---
# <a name="exclude-disks-from-replication"></a>从复制中排除磁盘

本文介绍如何在将 Hyper-V VM 复制到 Azure 时排除磁盘。 出于多种原因，你可能需要在复制时排除磁盘：

- 确保不会复制已排除磁盘上改动的非关键数据。
- 可以通过排除无需复制的磁盘来优化消耗的复制带宽或优化目标端资源。
- 需要节省存储和网络资源，因此不复制不需要的数据。

在将磁盘从复制中排除前，请执行以下操作：

- [详细了解](exclude-disks-replication.md)如何排除磁盘。
- 查看[典型的排除方案](exclude-disks-replication.md#typical-scenarios)和[示例](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)，它们介绍了排除磁盘对复制、故障转移和故障回复的影响。

## <a name="before-you-start"></a>开始之前

开始之前，请注意以下事项：

- **复制**：默认情况下将复制计算机上的所有磁盘。
- **磁盘类型**：
    - 可以从复制中排除基本磁盘。
    - 不能排除操作系统磁盘。
    - 建议不要排除动态磁盘。 Site Recovery 无法识别来宾 VM 上的 VHD 是基本磁盘还是动态磁盘。  如果未排除所有依赖性动态卷磁盘，则受保护的动态磁盘就会成为故障转移 VM 上的故障磁盘，且该磁盘上的数据无法访问。
- **添加/删除/排除磁盘**：启用复制后，无法添加/删除/排除要复制的磁盘。 如果想要添加/删除或排除磁盘，需要禁用 VM 保护，并重新启用保护。
- **故障转移**：故障转移后，如果故障转移应用需要排除磁盘才能运行，则需要手动创建这些磁盘。 或者，可将 Azure 自动化集成到恢复计划中，以便在故障转移计算机期间创建磁盘。
- **故障回复**：故障转移后故障回复到本地站点时，不会对 Azure 中手动创建的磁盘进行故障回复。 例如，如果在 Azure VM 中故障转移三个磁盘并直接创建两个磁盘，则只会故障回复完成故障转移的三个磁盘。 在故障回复中或 VM 的反向复制中，不能包括手动创建的磁盘。

## <a name="exclude-disks"></a>排除磁盘

1. 若要在对 Hyper-V VM [启用复制](./hyper-v-azure-tutorial.md)时排除磁盘，请选择要复制的 VM，然后在“启用复制” > “属性” > “配置属性”页上，查看“要复制的磁盘”列     。 默认情况下，选择所有磁盘进行复制。
2. 如果不想复制特定磁盘，请在“要复制的磁盘”中取消选择要排除的任何磁盘  。 

    ![从复制中排除磁盘](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>后续步骤
设置并运行部署后，请 [详细了解](failover-failback-overview.md) 不同类型的故障转移。

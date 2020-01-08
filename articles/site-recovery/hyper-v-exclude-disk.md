---
title: 通过 Azure Site Recovery 将 Hyper-v VM 磁盘从灾难恢复中排除到 Azure
description: 如何通过 Azure Site Recovery 将 Hyper-v VM 磁盘从复制中排除到 Azure。
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 50fb6da2905b2ae27547f25cce3d7a76ca7976b7
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498130"
---
# <a name="exclude-disks-from-replication"></a>从复制中排除磁盘

本文介绍在将 Hyper-v Vm 复制到 Azure 时如何排除磁盘。 出于多种原因，你可能需要从复制中排除磁盘：

- 确保排除的磁盘上改动不重要的数据不会被复制。
- 通过排除不需要复制的磁盘来优化消耗的复制带宽或目标端资源。
- 不复制不需要的数据，从而节省存储和网络资源。

从复制中排除磁盘之前：

- [详细了解](exclude-disks-replication.md)如何排除磁盘。
- 查看[典型的排除方案](exclude-disks-replication.md#typical-scenarios)和演示如何排除磁盘对复制、故障转移和故障回复的影响的[示例](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)。

## <a name="before-you-start"></a>开始之前

开始之前，请注意以下问题：

- **复制**：默认情况下，将复制计算机上的所有磁盘。
- **磁盘类型**：
    - 可以从复制中排除基本磁盘。
    - 不能排除操作系统磁盘。
    - 建议不要排除动态磁盘。 Site Recovery 无法识别来宾 VM 中哪个 VHD 是基本 VHD 或动态 VHD。  如果不排除所有相关动态卷磁盘，则受保护的动态磁盘会成为故障转移 VM 上的故障磁盘，且无法访问该磁盘上的数据。
- **添加/删除/排除磁盘**：启用复制后，无法添加/删除/排除复制的磁盘。 如果要添加/删除或排除磁盘，需要禁用 VM 保护，然后再次启用保护。
- **故障转移**：故障转移后，如果故障转移的应用程序需要排除磁盘才能运行，则需要手动创建这些磁盘。 或者，可以将 Azure 自动化集成到恢复计划中，以便在故障转移计算机期间创建磁盘。
- **故障回复**：故障转移后故障回复到本地站点后，在 Azure 中手动创建的磁盘不会故障回复。 例如，如果在 Azure VM 上故障转移三个磁盘并直接创建两个磁盘，则只有三个故障转移的磁盘会故障回复。 不能包括在故障回复或反向复制 Vm 时手动创建的磁盘。

## <a name="exclude-disks"></a>排除磁盘

1. 若要在为 Hyper-v VM[启用复制](site-recovery-hyper-v-site-to-azure.md)时排除磁盘，请在选择要复制的 vm 后，在 "**启用复制** > **属性**" > **配置属性**"页上，查看"**要复制的磁盘**"列。 默认情况下，选择所有磁盘进行复制。
2. 如果不想复制特定磁盘，请在 "**要复制的磁盘**" 中清除要排除的任何磁盘的所选内容。 

    ![从复制中排除磁盘](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>后续步骤
设置并运行部署后，请 [详细了解](failover-failback-overview.md) 不同类型的故障转移。

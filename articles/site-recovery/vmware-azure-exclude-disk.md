---
title: 通过 Azure Site Recovery 将 VMware VM 磁盘从灾难恢复中排除到 Azure
description: 如何通过 Azure Site Recovery 将 VMware VM 磁盘从复制中排除到 Azure。
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: cd54da5ee01206e576157435135065189bfb8035
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495364"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>从 VMware VM 到 Azure 的复制中排除磁盘

本文介绍了将 VMware Vm 复制到 Azure 以进行灾难恢复时如何排除磁盘。 出于多种原因，你可能需要从复制中排除磁盘：

- 确保排除的磁盘上改动不重要的数据不会被复制。
- 通过排除不需要复制的磁盘来优化消耗的复制带宽或目标端资源。
- 不复制不需要的数据，从而节省存储和网络资源。

从复制中排除磁盘之前：

- [详细了解](exclude-disks-replication.md)如何排除磁盘。
- 查看[典型的排除方案](exclude-disks-replication.md#typical-scenarios)和演示如何排除磁盘对复制、故障转移和故障回复的影响的[示例](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)。

## <a name="before-you-start"></a>开始之前

 开始之前，请注意以下问题：

- **复制**：默认情况下，将复制计算机上的所有磁盘。
- **磁盘类型**：只能从复制中排除基本磁盘。 不能排除操作系统磁盘或动态磁盘。
- **移动服务**：若要从复制中排除磁盘，必须先在计算机上手动安装移动服务，然后才能启用复制。 不能使用推送安装，因为此方法仅在启用复制后在 VM 上安装移动服务。  
- **添加/删除/排除磁盘**：启用复制后，无法添加/删除/排除复制的磁盘。 如果要添加/删除或排除磁盘，需要禁用计算机保护，然后再次启用保护。
- **故障转移**：故障转移后，如果故障转移的应用程序需要排除的磁盘才能正常运行，则需要手动创建这些磁盘。 或者，可以将 Azure 自动化集成到恢复计划中，以便在故障转移计算机期间创建磁盘。
- **故障回复-Windows**：故障转移后故障回复到本地站点后，在 Azure 中手动创建的 Windows 磁盘不会故障回复。 例如，如果在 Azure Vm 上故障转移三个磁盘并直接创建两个磁盘，则只会故障回复三个故障转移的磁盘。
- **故障回复-linux**：对于 Linux 计算机，在 Azure 中手动创建的磁盘将故障回复。 例如，如果在 Azure Vm 上故障转移三个磁盘并直接创建两个磁盘，则将故障回复所有五个磁盘。 不能排除在故障回复或 Vm 重新保护中手动创建的磁盘。



## <a name="exclude-disks-from-replication"></a>从复制中排除磁盘

1. 为 VMware VM[启用复制](site-recovery-hyper-v-site-to-azure.md)后，在选择要复制的 vm 后，在 "**启用复制** > **属性**" > **配置属性**"页上，查看"**要复制的磁盘**"列。 默认情况下，选择所有磁盘进行复制。
2. 如果不想复制特定磁盘，请在 "**要复制的磁盘**" 中清除要排除的任何磁盘的所选内容。 

    ![从复制中排除磁盘](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>后续步骤
设置并运行部署后，请 [详细了解](failover-failback-overview.md) 不同类型的故障转移。

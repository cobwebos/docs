---
title: Linux VM 备份选项概述
description: 概述 Linux Azure 虚拟机的备份选项。
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 8/03/2020
ms.author: cynthn
ms.openlocfilehash: c56adc852b504d3a0663a9607b3472cb3348e7a5
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972350"
---
# <a name="backup-and-restore-options-for-linux-virtual-machines-in-azure"></a>Azure 中 Linux 虚拟机的备份和还原选项

可以通过定期创建备份来保护数据。 有多个备份选项可用于 VM，具体取决于使用案例。

## <a name="azure-backup"></a>Azure 备份

若要备份运行生产工作负荷的 Azure VM，请使用 Azure 备份。 Azure 备份对 Windows 和 Linux VM 均支持应用程序一致性备份。 Azure 备份可创建恢复点，这些恢复点存储在异地冗余的恢复保管库中。 从恢复点还原时，可以还原整个 VM，也可以仅还原特定的文件。 

有关适用于 Azure VM 的 Azure 备份动手实践简介，请参阅适用于 [Linux](./linux/tutorial-backup-vms.md) 或 [Windows](./windows/tutorial-backup-vms.md) 的“备份 Azure 虚拟机”教程。

有关 Azure 备份工作原理的详细信息，请参阅[在 Azure 中计划 VM 备份基础结构](../backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

当整个区域因重大自然灾难或大规模服务中断而发生中断时，Azure Site Recovery 可以保护 VM，使其免受重大灾难影响。 可以为 VM 配置 Azure Site Recovery，只需单击一下，几分钟内即可恢复应用程序。 可复制到所选的 Azure 区域（并不局限于配对区域）。 

可以通过按需测试故障转移进行灾难恢复演练，而不影响生产工作负荷或正在进行的复制。 创建恢复计划，为多个 VM 上运行的整个应用程序安排故障转移和故障回复。 恢复计划功能将与 Azure 自动化 Runbook 集成。

可以通过[复制虚拟机](../site-recovery/azure-to-azure-quickstart.md)来启动。 

## <a name="managed-snapshots"></a>托管快照 

在开发和测试环境中，快照为使用托管磁盘的 VM 备份提供快速而又简单的选项。 托管快照是托管磁盘的只读完整副本。 快照独立于源磁盘而存在，并可用来新建用于重建 VM 的托管磁盘。 基于磁盘的已使用部分对快照进行计费。 例如，如果创建预配容量为 64 GB、实际使用数据大小为 10 GB 的托管磁盘的快照，将仅针对已用数据大小 10 GB 对该快照进行计费。  

有关创建快照的详细信息，请参阅：

* [使用 Windows 中的快照创建作为托管磁盘存储的 VHD 的副本](./windows/snapshot-copy-managed-disk.md)
* [使用 Linux 中的快照创建作为托管磁盘存储的 VHD 的副本](./linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>后续步骤
可以按照适用于 [Linux](./linux/tutorial-backup-vms.md) 或 [Windows](./windows/tutorial-backup-vms.md) 的“备份 Windows 虚拟机教程”试用 Azure 备份。
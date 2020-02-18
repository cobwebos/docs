---
title: 有关 Azure Migrate 服务器迁移的常见问题
description: 获取有关 Azure Migrate 服务器迁移迁移计算机的常见问题的解答
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0c967027457b925b45ea19d994cfadfdbd0b8ab3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425827"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server 迁移：常见问题

本文解答了有关 Azure Migrate： Server 迁移工具的常见问题。 如果在阅读本文后有更多问题，请查看以下文章：

- 有关 Azure Migrate 的[一般问题](resources-faq.md)。
- 关于 Azure Migrate 设备的[问题](common-questions-appliance.md)。
- 有关发现、评估和依赖项可视化的[问题](common-questions-discovery-assessment.md)。
- 在[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)上发布问题。


## <a name="how-does-agentless-vmware-replication-work"></a>无代理 VMware 复制的工作原理是什么？

VMware 的无代理复制方法使用 VMware 快照，而 VMware 更改了阻止跟踪（CBT）。

1. 启动复制时，将安排初始复制循环。 在初始循环中，会创建 VM 的快照。 此快照用于复制 Vm Vmdk （磁盘）。 
2. 初始复制周期结束后，将定期计划增量复制循环。
    - 在增量复制过程中，将会创建快照，并复制自上次复制循环以来发生更改的数据块。
    - VMware CBT 用于确定自上次循环后已更改的块。
    - 定期复制循环的频率由 Azure Migrate 自动管理，具体取决于从同一数据存储同时复制的其他 Vm/磁盘的数量。 在理想情况下，对于每个 VM，复制最终聚合为每小时一个周期。

在迁移时，将为计算机安排按需复制循环，以捕获所有剩余数据。 你可以选择在迁移过程中关闭计算机，以确保零数据丢失和应用程序一致性。

## <a name="why-isnt-resynchronization-exposed"></a>为什么未公开同步？

在无代理迁移期间，将写入当前快照和之前拍摄的快照之间的差异。 由于快照始终是快照之间的差异，因此请在中折叠数据。 这样，如果在快照之间写入特定扇区为 N 次，则只需要传输最后一次写入，因为我们只对上次同步感兴趣。这不同于基于代理的复制，我们跟踪并应用每次写入。 这意味着每个增量周期都是重新同步。 因此，没有公开同步选项。 如果由于故障而导致磁盘不同步，则会在下一个周期中进行修复。 

## <a name="how-does-churn-rate-impact-agentless-replication"></a>变动率如何影响无代理复制？

由于无代理复制折叠日期，变动模式更重要。 再次编写文件时，速率不会产生很大的影响。 但是，每个其他扇区的写入模式在下一个周期中会导致较高的变动。 由于我们最大限度地减少了传输的数据量，因此，我们允许数据在计划下一个周期之前折叠尽可能多。  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>复制循环计划的频率如何？

用于计划下一个复制周期的公式：（上一个周期时间/2）或1小时，取两者中的较大者。

例如，如果某个 VM 的增量周期使用四个小时，则将在两个小时内计划下一个周期，而不是在下一个小时。 这在初始复制后会立即发生变化，在这种情况下，将立即计划第一个增量循环。

## <a name="how-does-agentless-replication-impact-vmware-servers"></a>无代理复制如何影响 VMware 服务器？

VCenter Server/ESXi 主机会对性能产生一定的影响。 由于无代理复制使用快照，因此它会在存储上使用 IOPs，并且需要一些 IOPS 存储带宽。 如果对环境中的存储/IOPs 有限制，我们不建议使用无代理复制。

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>是否可以进行 UEFI Vm 到 Azure 第2代的无代理迁移？

No。 使用 Azure Site Recovery 将这些虚拟机迁移到第2代 Azure Vm。 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>迁移时，是否可以将 Vm 固定到 Azure 可用性区域？

不支持，Azure 可用性区域不受支持。

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>在复制过程中 Azure Migrate 使用哪种传输协议？

Azure Migrate 将网络块设备（NBD）协议与 SSL 加密配合使用。

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>迁移需要的最低 vCenter Server 版本是什么？

至少需要 vCenter Server 5.5 和 VMware vSphere ESXi 主机版本5.5。

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>客户是否可以将其虚拟机迁移到非托管磁盘？

No。 Azure Migrate 仅支持对托管磁盘（标准 HDD、高级 SSD）进行迁移。

## <a name="how-many-vms-can-i-replicate-together-with-agentless-migration"></a>我可以将多少个 Vm 复制到无代理迁移？

目前，可以同时迁移每个 vCenter Server 的 100 Vm。 成批迁移10个虚拟机。
 




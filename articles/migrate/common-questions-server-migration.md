---
title: 有关 Azure Migrate 服务器迁移的常见问题
description: 获取有关 Azure Migrate 服务器迁移的常见问题的解答
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: bae3447f0fada18de5473e1ef1a1c1d431535f63
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77067378"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server 迁移：常见问题

本文解答了有关 Azure Migrate：服务器迁移的常见问题。 阅读本文后，如果你有更多的查询，请将其发布到[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)。 如果你有其他问题，请查看以下文章：

- 有关 Azure Migrate 的[一般问题](resources-faq.md)。
- 关于 Azure Migrate 设备的[问题](common-questions-appliance.md)。
- 有关发现、评估和依赖项可视化的[问题](common-questions-discovery-assessment.md)。


## <a name="how-does-agentless-vmware-replication-work"></a>无代理 VMware 复制的工作原理是什么？

VMware 的无代理复制方法使用 VMware 快照，而 VMware 更改了阻止跟踪（CBT）。 当用户启动复制时，将安排初始复制循环。 在初始复制循环中，会创建 VM 的快照，并使用此快照复制 Vm Vmdk （磁盘）。 初始复制循环完成后，将定期计划增量复制循环。 在增量复制循环中，会创建快照，并复制自上次复制循环以来发生更改的数据块。 VMware 更改的块跟踪用于确定自上次循环后已更改的块。
定期复制循环的频率由服务自动管理，具体取决于其他 Vm/磁盘并发复制到相同数据存储的数量，理想情况中，每个 VM 的每小时最终聚合为1个循环。

在迁移时，计划 VM 的按需复制周期，以捕获所有剩余数据。 你可以选择在迁移过程中关闭 VM，以确保零数据丢失和应用程序一致性。

## <a name="why-is-the-resynchronization-option-not-exposed-in-agentless-stack"></a>为什么无代理堆栈中未公开重新同步选项？

在无代理堆栈中，在每个增量循环中，我们会在当前快照与我们以前拍摄的快照之间传输差异。 因为快照始终是快照之间的差异，所以这可以使数据折叠（即，如果在快照之间写入特定扇区 "n" 次，我们只需要传输最后一次写入，因为我们仅在上次同步时才有兴趣）。 这不同于基于代理的堆栈，我们在其中跟踪每个写入并对其应用。 这意味着每个增量周期都是重新同步。 因此，没有公开同步选项。 

如果磁盘因故障而不同步，则会在下一个周期中修复。 

## <a name="what-is-the-impact-of-churn-rate-if-i-use-agentless-replication"></a>如果使用无代理复制，变动率会产生什么影响？

由于堆栈依赖于要折叠的数据，因此超出了变动率，变动模式是此堆栈中的重要内容。 一种模式，在该模式中再次编写文件，而不会产生很大的影响。 但是，在这种模式下，每个其他扇区的写入将导致下一个周期中发生较高的变动。 由于我们最大限度地减少了传输的数据量，因此，我们允许数据在计划下一个周期之前折叠尽可能多。  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>复制循环计划的频率如何？

计划下一个复制周期的公式如下所示：（上一个周期时间/2）或1小时以上。 例如，如果某个 VM 的增量周期花费了四个小时，我们将在2小时内计划其下一个周期，而不是在下一个小时。 这在以下情况下不同：循环在 IR 之后立即进行，我们将立即计划第一个增量循环。

## <a name="what-is-the-impact-on-performance-of-vcenter-server-or-esxi-host-while-using-agentless-replication"></a>使用无代理复制时，对 vCenter Server 或 ESXi 主机的性能有什么影响？

由于无代理复制使用快照，因此在存储上会消耗 IOPs，并且客户需要在存储空间上使用一些 IOPs 空间。 不建议在存储/IOPs 约束的环境上使用此堆栈。

## <a name="does-agentless-migration-stack-support-migration-of-uefi-vms-to-azure-gen-2-vms"></a>无代理迁移堆栈是否支持将 UEFI Vm 迁移到 Azure 第2代 Vm？

不需要，你必须使用 Azure Site Recovery 将这些虚拟机迁移到第2代 Azure Vm。 

## <a name="can-i-pin-my-vms-to-azure-availability-zones-when-i-migrate"></a>迁移时，是否可以将 Vm 固定到 Azure 可用性区域？

否，不支持 Azure 可用性区域。

## <a name="which-transport-protocol-is-used-by-azure-migrate-during-replication"></a>在复制过程中 Azure Migrate 使用哪种传输协议？

Azure Migrate 将网络块设备（NBD）协议与 SSL 加密配合使用。

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>迁移需要的最低 vCenter Server 版本是什么？

至少需要 vCenter Server 5.5 和 VMware vSphere ESXi 主机版本5.5。

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>客户是否可以将其虚拟机迁移到非托管磁盘？

No。 Azure Migrate 仅支持对托管磁盘（标准 HDD、高级 SSD）进行迁移。

## <a name="how-many-vms-can-replicate-simultaneously-using-agentless-vmware-stack"></a>使用无代理 VMware 堆栈可以同时复制多少个 Vm？

目前，客户可以同时迁移每个 vCenter Server 的 100 Vm。 这可通过10个 Vm 批量完成。





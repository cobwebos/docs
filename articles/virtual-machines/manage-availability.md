---
title: 管理 VM 的可用性
description: 了解如何在 Azure 中使用多个虚拟机来确保应用程序的高可用性
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cynthn
ms.openlocfilehash: fe89b58f71b14b211863fd46ba523e8c866764f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361783"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>管理 Linux 虚拟机的可用性

了解如何设置和管理多个虚拟机，以确保 Linux 应用程序在 Azure 中的高可用性。 


## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>了解 VM 重启 - 维护和停机
有三种情况可能会导致 Azure 中的虚拟机受影响：计划外硬件维护、意外停机、计划内维护。

* 当 Azure 平台预测硬件或者与物理计算机关联的任何平台组件即将发生故障时，就会发生计划外硬件维护事件。 当预测到故障时，平台会发出计划外硬件维护事件，以便减少对托管在该硬件上的虚拟机的影响。 Azure 使用[实时迁移](./linux/maintenance-and-updates.md)技术将虚拟机从故障硬件迁移到正常的物理计算机。 实时迁移是一项 VM 保留操作，只能短时间暂停虚拟机。 将会保留内存、打开的文件以及网络连接，但事件前后的性能可能会降低。 在无法使用实时迁移的情况下，VM 会出现意外停机，如下所述。


* 意外停机指虚拟机的硬件或物理基础设施意外出现故障。 此类故障可能包括：本地网络故障、本地磁盘故障，或者其他机架级别的故障。 检测到此类故障时，Azure 平台会自动将虚拟机迁移到同一数据中心内的正常物理机（进行修复）。 在修复过程中，虚拟机会经历停机（重启），在某些情况下会丢失临时驱动器。 始终会保留附加的 OS 和数据磁盘。

  在发生会影响整个数据中心甚至整个区域的服务中断或灾难时（这种情况很少见），虚拟机也可能会停机。 针对这种情况，Azure 提供了保护选项，包括[可用性区域](../availability-zones/az-overview.md)和[配对区域](regions.md#region-pairs)。

* 计划内维护事件是指由 Microsoft 对底层 Azure平台进行定期更新，以改进虚拟机运行时所在的平台基础结构的总体可靠性、性能和安全性。 大多数此类更新在执行时不会对虚拟机或云服务产生任何影响 (请参阅 [不需要重新启动) 的维护](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot) 。 虽然 Azure 平台会尝试在所有可能的情况下都使用 VM 保留维护，但在罕见情况下，这些更新需要重启虚拟机，否则无法将所需更新应用到底层基础结构。 在这种情况下，可以在合适的时间窗口为 VM 启动维护，通过“维护-重新部署”操作来执行 Azure 计划内维护。 有关详细信息，请参阅[虚拟机的计划内维护](maintenance-and-updates.md)。


要减轻一个或多个此类事件引发的停机所造成的影响，我们建议遵循以下最佳做法以提高虚拟机的可用性：

* 使用可用性区域防止数据中心故障
* 在可用性集中配置多个虚拟机以确保冗余
* 为可用性集中的 VM 使用托管磁盘
* 使用计划事件主动响应影响事件的 VM
* 将每个应用程序层配置到不同的可用性集中
* 将负载均衡器与可用性区域或可用性集组合在一起
* 使用可用性区域防范数据中心级故障

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>使用可用性区域防范数据中心级故障

[可用性区域](../availability-zones/az-overview.md)扩展了在保持 VM 上应用程序和数据的可用性时的控制级别。 可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保复原能力，所有已启用的区域中必须至少有三个单独的区域。 区域中可用性区域的物理隔离可以在发生数据中心故障的情况下保护应用程序和数据。 区域冗余服务可跨可用性区域复制应用程序和数据，以防范单点故障。

Azure 区域中的可用性区域是容错域和更新域的组合 。 例如，如果在 Azure 区域的三个区域中创建三个或更多 VM，则 VM 将有效分布在三个容错域和三个更新域中。 Azure 平台会识别更新域上的此分布，以确保不同区域中的 VM 不会同时更新。

Azure 凭借可用性区域提供一流的 99.99% VM 运行时间 SLA。 通过将解决方案构建为使用区域中复制的 VM，可以在数据中心服务中断时保护应用程序和数据。 如果一个区域发生故障，另一个区域会立即提供复制的应用和数据。

![可用性区域](./media/virtual-machines-common-manage-availability/three-zones-per-region.png)

详细了解如何在可用性区域中部署 [Windows](./windows/create-powershell-availability-zone.md) 或 [Linux](./linux/create-cli-availability-zone.md) VM。

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>在可用性集中配置多个虚拟机以确保冗余
可用性集是另一种数据中心配置，用于提供 VM 冗余和可用性。 数据中心内的这种配置可以确保在发生计划内或计划外维护事件时，至少有一个虚拟机可用，并满足 99.95% 的 Azure SLA 要求。 有关详细信息，请参阅[虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。

> [!IMPORTANT]
> 可用性集中的单个实例虚拟机本身应对所有操作系统磁盘和数据磁盘使用高级 SSD 或超级磁盘，以便满足虚拟机连接至少达到 99.9% 的 SLA 要求。 
> 
> 具有标准 SSD 的单实例虚拟机将具有至少 99.5% 的 SLA，而具有标准 HDD 的单实例虚拟机将具有至少 95% 的 SLA。  请参阅[虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)

基础 Azure 平台为可用性集中的每个虚拟机分配一个更新域和一个容错域 。 对于给定的可用性集，默认情况下会分配五个非用户可配置的更新域（可以增加 Resource Manager 部署以最多提供 20 个更新域），以指示可同时重新启动的虚拟机和底层物理硬件组。 如果单个可用性集中配置了超过 5 个虚拟机，第 6 个虚拟机放置在第 1 个虚拟机所在的更新域中，第 7 个虚拟机放置在第 2 个虚拟机所在的更新域中，依此类推。 在计划内维护期间，更新域的重启顺序可能不会按序进行，但一次只重启一个更新域。 重启的更新域有 30 分钟的时间进行恢复，此时间过后，就会在另一更新域上启动维护操作。

容错域定义一组共用一个通用电源和网络交换机的虚拟机。 默认情况下，在可用性集中配置的虚拟机隔离在 Resource Manager 部署的最多三个容错域（经典部署的两个容错域）中。 虽然将虚拟机置于可用性集中并不能让应用程序免受特定于操作系统或应用程序的故障的影响，但可以限制潜在物理硬件故障、网络中断或电源中断的影响。

<!--Image reference-->
   ![更新域和容错域配置的概念图](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>为可用性集中的 VM 使用托管磁盘
如果你当前正在使用包含非托管磁盘的 Vm，我们强烈建议你从非托管磁盘转换为适用于 [Linux](./linux/convert-unmanaged-to-managed-disks.md) 和 [Windows](./windows/convert-unmanaged-to-managed-disks.md)的托管磁盘。

通过确保可用性集中的 VM 的磁盘彼此之间完全隔离以避免单点故障，[托管磁盘](./managed-disks-overview.md)为可用性集提供了更佳的可靠性。 为此，会自动将磁盘放置在不同的存储容错域（存储群集）中，并使它们与 VM 容错域一致。 如果某个存储容错域因硬件或软件故障而失败，则只有其磁盘在该存储容错域上的 VM 实例会失败。
![托管磁盘 FD](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> 托管可用性集的容错域的数目因区域而异 - 每个区域两到三个。 通过运行以下脚本，可查看每个区域的容错域。

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> 在某些情况下，同一可用性集中的两个 VM 可能会共享一个容错域。 可以通过转到可用性集并查看“容错域”列来确认某个共享容错域。 在部署 VM 时，完成以下操作序列可能导致共享容错域：
> 1. 部署第一个 VM。
> 1. 停止/解除分配第一个 VM。
> 1. 部署第二个 VM。
>
> 在这些情况下，第二个 VM 的 OS 磁盘可能会在第一个 VM 所在的容错域上创建，从而导致两个 VM 位于同一个容错域中。 为避免此问题，建议不要在部署之间停止/解除分配 VM。

如果计划使用包含非托管磁盘的 VM，请按下述针对存储帐户的最佳做法进行操作。在这些存储帐户中，VM 的虚拟硬盘 (VHD) 以[页 Blob](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) 形式存储。

1. 将与同一 VM 关联的所有磁盘（OS 和数据）放置在同一存储帐户中
2. 在向存储帐户中添加更多 VHD 之前，请**查看 Azure 存储帐户中非托管磁盘的数量[限制](../storage/blobs/scalability-targets-premium-page-blobs.md)**
3. **为可用性集中的每个 VM 使用单独的存储帐户。** 同一可用性集中的多个 VM 不能共享存储帐户。 不同可用性集中的 VM 共享存储帐户是可以接受的，只要遵循上述最佳做法即可 ![托管磁盘 FD](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>使用计划事件主动响应影响事件的 VM

如果订阅[计划事件](./linux/scheduled-events.md)，则将通知 VM 即将发生会对 VM 造成影响的维护事件。 启用计划事件后，可在执行维护活动之前为虚拟机提供最少的时间。 例如，可能会影响 VM 的主机 OS 更新将作为事件排队等候，通知中将详述其影响，以及在未采取任何操作的情况下执行维护的时间。 当 Azure 检测到即将发生可能影响 VM 的硬件失败时，计划事件也会排队等候，以便决定执行修复的时间。 客户可以使用事件在维护前执行任务，例如，保存状态、故障转移到辅助 VM 等。 完成用于妥善处理维护事件的逻辑后，可批准未完成的计划事件，以允许平台继续进行维护。


## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>将负载均衡器与可用性区域或可用性集组合在一起
将 [Azure 负载均衡器](../load-balancer/load-balancer-overview.md)与可用性区域或可用性集组合在一起，以获取最高的应用程序复原能力。 Azure 负载均衡器将流量分布到多个虚拟机中。 对于标准层虚拟机来说，Azure 负载均衡器已包括在内。 并非所有虚拟机层都包括 Azure 负载均衡器。 有关对虚拟机进行负载均衡的详细信息，请参阅对[Linux](linux/tutorial-load-balancer.md)或[Windows](windows/tutorial-load-balancer.md)的**虚拟机进行负载平衡**。

如果没有将负载均衡器配置为对多个虚拟机上的流量进行平衡，则任何计划内维护事件都会影响唯一的那个处理流量的虚拟机，导致应用程序层中断。 将同一层的多个虚拟机置于相同的负载均衡器和可用性集下可以确保至少有一个虚拟机实例能够持续处理流量。

有关如何在可用性区域之间进行负载平衡的教程，请参阅 [教程：使用 Azure 门户标准负载均衡器负载平衡可用性区域](../load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal.md)中的 vm。


## <a name="next-steps"></a>后续步骤
若要了解对虚拟机进行负载均衡的详细信息，请参阅[对虚拟机进行负载均衡](../load-balancer/load-balancer-overview.md)。

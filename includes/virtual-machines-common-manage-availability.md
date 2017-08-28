## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>了解 VM 重启 - 维护和停机
有三种情况可能会导致 Azure 中的虚拟机受影响：计划外硬件维护、意外停机、计划内维护。

* 当 Azure 平台预测硬件或者与物理计算机关联的任何平台组件即将发生故障时，就会发生计划外硬件维护事件。 当预测到故障时，平台会发出计划外硬件维护事件，以便减少对托管在该硬件上的虚拟机的影响。 Azure 使用实时迁移技术将虚拟机从故障硬件迁移到健康的物理计算机。 实时迁移是一项 VM 保留操作，只能短时间暂停虚拟机。 将会保留内存、打开的文件以及网络连接，但事件前后的性能可能会降低。 在无法使用实时迁移的情况下，VM 会出现意外停机，如下所述。


* 意外停机很少发生在虚拟机所在硬件或物理基础结构出现某类故障的情况。 此类故障可能包括：本地网络故障、本地磁盘故障，或者其他机架级别的故障。 检测到此类故障时，Azure 平台会自动将虚拟机迁移到正常的物理计算机（进行修复）。 在修复过程中，虚拟机会经历停机（重启），在某些情况下会丢失临时驱动器。 始终会保留附加的 OS 和数据磁盘。 

* 计划内维护事件是指由 Microsoft 对底层 Azure平台进行定期更新，以改进虚拟机运行时所在的平台基础结构的总体可靠性、性能和安全性。 大多数此类更新在执行时不会影响虚拟机或云服务（请参阅 [VM 保留维护](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/preserving-maintenance)）。 虽然 Azure 平台会尝试在所有可能的情况下都使用 VM 保留维护，但在罕见情况下，这些更新需要重启虚拟机，否则无法将所需更新应用到底层基础结构。 在这种情况下，可以在合适的时间窗口为 VM 启动维护，通过“维护-重新部署”操作来执行 Azure 计划内维护。 有关详细信息，请参阅 [Planned Maintenance for Virtual Machines](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/planned-maintenance/)（虚拟机的计划内维护）。


要减轻一个或多个此类事件引发的停机所造成的影响，我们建议遵循以下最佳做法以提高虚拟机的可用性：

* [在可用性集中配置多个虚拟机以确保冗余]
* [在可用性集中对 VM 使用托管磁盘]
* [使用计划的事件对影响 VM 的事件进行主动响应] (https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-scheduled-events)
* [将每个应用程序层配置到不同的可用性集中]
* [将负载均衡器与可用性集组合在一起]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>在可用性集中配置多个虚拟机以确保冗余
要为应用程序提供冗余，建议将两个或更多虚拟机组合到一个可用性集中。 这种配置可以确保在发生计划内或计划外维护事件时，至少有一个虚拟机可用，并满足 99.95% 的 Azure SLA 要求。 有关详细信息，请参阅[虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。

> [!IMPORTANT]
> 避免将单实例虚拟机单独地置于可用性集中。 此配置中的 VM 并不符合 SLA 保证，在出现 Azure 计划内维护事件时会停机，除非某个 VM 正在使用 [Azure 高级存储](../articles/storage/common/storage-premium-storage.md)。 对于使用高级存储的单一 VM，Azure SLA 适用。

基础 Azure 平台为可用性集中的每个虚拟机分配一个**更新域**和一个**容错域**。 对于给定的可用性集，默认情况下会分配五个非用户可配置的更新域（可以增加 Resource Manager 部署以最多提供 20 个更新域），以指示可同时重新启动的虚拟机和底层物理硬件组。 在单个可用性集中配置了 5 个以上的虚拟机时，第 6 个虚拟机将放置在第 1 个虚拟机所在的更新域中，第 7 个虚拟机将放置在第 2 个虚拟机所在的更新域中，依此类推。 在计划内维护期间，更新域的重启顺序可能不会按序进行，但一次只重启一个更新域。 重启的更新域有 30 分钟的时间进行恢复，此时间过后，就会在另一更新域上启动维护操作。

容错域定义一组共用一个通用电源和网络交换机的虚拟机。 默认情况下，在可用性集中配置的虚拟机隔离在 Resource Manager 部署的最多三个容错域（经典部署的两个容错域）中。 虽然将虚拟机置于可用性集中并不能让应用程序免受特定于操作系统或应用程序的故障的影响，但可以限制潜在物理硬件故障、网络中断或电源中断的影响。

<!--Image reference-->
   ![更新域和容错域配置的概念图](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>为可用性集中的 VM 使用托管磁盘
如果当前使用的 VM 没有托管磁盘，则强烈建议[在可用性集中转换 VM，以便使用托管磁盘](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md)。

通过确保可用性集中的 VM 的磁盘彼此之间完全隔离以避免单点故障，[托管磁盘](../articles/virtual-machines/windows/managed-disks-overview.md)为可用性集提供了更佳的可靠性。 它通过自动将磁盘放置在不同的存储群集中来实现这一点。 如果某个存储群集因硬件或软件故障而失败，则只有其磁盘在该模块上的 VM 实例会失败。

![托管磁盘 FD](./media/virtual-machines-common-manage-availability/md-fd.png)

> [!IMPORTANT]
> 托管可用性集的容错域的数目因区域而异 - 每个区域两到三个。 下表显示了每个区域的数目

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

如果计划使用包含[非托管磁盘](../articles/virtual-machines/windows/about-disks-and-vhds.md#types-of-disks)的 VM，请按下述针对存储帐户的最佳做法进行操作。在这些存储帐户中，VM 的虚拟硬盘 (VHD) 以[页 Blob](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) 形式存储。

1. **将与同一 VM 关联的所有磁盘（OS 和数据）放置在同一存储帐户中**
2. 在向存储帐户添加更多 VHD 之前，请**查看存储帐户中非托管磁盘的数量[限制](../articles/storage/common/storage-scalability-targets.md)**
3. **为可用性集中的每个 VM 使用单独的存储帐户。** 同一可用性集中的多个 VM 不能共享存储帐户。 不同可用性集中的 VM 共享存储帐户是可以接受的，只要遵循上述最佳做法即可

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>将每个应用程序层配置到不同的可用性集中
如果虚拟机几乎都是相同的，并且对应用程序的用途是一样的，我们建议针对每个应用程序层配置可用性集。  如果将两个不同的层置于同一可用性集中，则同一应用程序层中的所有虚拟机可以同时重启。 通过在可用性集中为每个层配置至少两个虚拟机，可以确保每个层中至少有一个虚拟机可用。

例如，可以将运行 IIS、Apache、Nginx 的应用程序前端的所有虚拟机置于单个可用性集中。 请确保仅将前端虚拟机置于同一可用性集中。 同样，请确保仅将数据层虚拟机置于其自身的可用性集中，例如已复制的 SQL Server 虚拟机或 MySQL 虚拟机。

<!--Image reference-->
   ![应用程序层](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>将负载均衡器与可用性集组合在一起
将 [Azure 负载均衡器](../articles/load-balancer/load-balancer-overview.md) 与可用性集组合在一起，以获取最大的应用程序复原能力。 Azure 负载均衡器将流量分布到多个虚拟机中。 对于标准层虚拟机来说，Azure 负载均衡器已包括在内。 并非所有虚拟机层都包括 Azure 负载均衡器。 有关对虚拟机进行负载均衡的更多信息，请阅读[对虚拟机进行负载均衡](../articles/virtual-machines/virtual-machines-linux-load-balance.md)。

如果没有将负载均衡器配置为对多个虚拟机上的流量进行平衡，则任何计划内维护事件都会影响唯一的那个处理流量的虚拟机，导致应用程序层中断。 将同一层的多个虚拟机置于相同的负载均衡器和可用性集下可以确保至少有一个虚拟机实例能够持续处理流量。


<!-- Link references -->
[在可用性集中配置多个虚拟机以确保冗余]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[将每个应用程序层配置到不同的可用性集中]: #configure-each-application-tier-into-separate-availability-sets
[将负载均衡器与可用性集组合在一起]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[在可用性集中对 VM 使用托管磁盘]: #use-managed-disks-for-vms-in-an-availability-set

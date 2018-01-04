
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Azure IaaS 磁盘的备份和灾难恢复

本文介绍如何规划 Azure 中的 IaaS 虚拟机 (VM) 和磁盘的备份与灾难恢复 (DR)。 本文档涉及托管磁盘和非托管磁盘。

首先介绍 Azure 平台内置的容错功能，此功能有助于预防本地故障的发生。 然后介绍内置功能未全面涵盖的灾难恢复方案。 这是本文档探讨的主要主题。 此外，本文档演示了几个工作负荷方案示例，它们的备份和 DR 注意事项各不相同。 最后，介绍适用于 IaaS 磁盘 DR 的可行解决方案。 

## <a name="introduction"></a>介绍

Azure 平台使用各种方法实现冗余和容错，以帮助客户避免本地硬件故障。 本地故障可能包括存储部分虚拟磁盘数据的 Azure 存储服务器计算机出现问题，或此服务器上的 SSD 或 HDD 发生故障。 此类隔离的硬件组件故障可能会在正常操作期间发生。 

Azure 平台旨在从这些故障中复原。 重大灾难可能会导致大量存储服务器甚至整个数据中心发生故障或无法访问。 尽管本地故障通常不会对 VM 和磁盘造成影响，但有必要采取额外措施，以保护工作负荷不受整个区域内的灾难性故障（如重大灾难）影响，此类故障可能会影响 VM 和磁盘。

除了可能出现的平台故障外，客户应用程序或数据也可能会出现问题。 例如，应用程序的新版本可能会在无意间对数据进行更改，导致应用程序中断。 在这种情况下，我们建议将应用程序和数据还原到上一版已知的良好状态。 这需要维护定期备份。

对于区域性灾难恢复，必须在不同区域中备份 IaaS VM 磁盘。 

在了解备份和 DR 选项之前，先来回顾一下本地故障的一些处理方法。

### <a name="azure-iaas-resiliency"></a>Azure IaaS 复原

复原是指对硬件组件中发生的常见故障实现容错。 通过复原，可以从故障中恢复，并继续正常运行。 复原并不旨在避免故障发生，而是通过响应故障来避免故障时间或数据丢失。 复原的目标是在故障发生后将应用程序恢复到可完全正常运行的状态。 Azure 虚拟机和磁盘旨在从常见硬件故障中复原。 让我们来看看 Azure IaaS 平台是如何提供这种复原功能的。

虚拟机主要由以下两部分组成：计算服务器和永久性磁盘。 这两部分都会影响虚拟机的容错。

如果托管 VM 的 Azure 计算主机服务器遇到硬件故障（极少数情况），那么 Azure 会在另一台服务器上自动还原 VM。 如果发生这种情况，计算机会重新启动，并在一段时间后备份 VM。 为了确保客户 VM 尽快可用，Azure 自动检测此类硬件故障，并执行恢复。

对于 IaaS 磁盘，数据持续性对永久性存储平台来说至关重要。 Azure 客户在 IaaS 上运行重要的商业应用程序，这些应用程序依赖数据的永久性。 Azure 针对这些 IaaS 磁盘设计的保护措施是在本地存储数据的三个冗余副本。 这些副本提供高持续性来应对本地故障。 如果托管磁盘的硬件组件之一出现故障，VM 不会受到影响，因为有两个额外的副本支持磁盘请求。 即使两个支持磁盘的不同硬件组件同时出现故障（这非常罕见），VM 也能正常运行。 

为了确保始终维护三个副本，Azure 存储在后台自动生成一个新数据副本，以防三个副本中有一个无法使用。 因此，不应通过结合使用 RAID 与 Azure 磁盘来实现容错。 简单的 RAID 0 配置应足以对磁盘进行必要分区，以创建更大的卷。

鉴于此体系结构，Azure 为 IaaS 磁盘不间断提供企业级数据持续性，[年化故障率](https://en.wikipedia.org/wiki/Annualized_failure_rate)为 0%，达到行业领先水平。

计算主机或存储平台上的本地硬件故障有时可能会导致 VM 暂时不可用，有关 VM 可用性的 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 对此做了介绍。 Azure 还提供了有关使用 Azure 高级存储磁盘的单 VM 实例的行业领先 SLA。

为了保护应用程序工作负荷不受磁盘或 VM 暂时不可用带来的故障时间影响，客户可以使用[可用性集](../articles/virtual-machines/windows/manage-availability.md)。 可用性集中的两个或多个虚拟机为应用程序提供冗余。 然后，Azure 在电源、网络和服务器组件不同的单独容错域中创建这些 VM 和磁盘。 

由于这些单独的容错域，本地硬件故障通常不会同时影响可用性集中的多个 VM。 单独的容错域为应用程序提供了高可用性。 如果需要高可用性，最好使用可用性集。 下一部分介绍灾难恢复方面。

### <a name="backup-and-disaster-recovery"></a>备份和灾难恢复

灾难恢复是指能够从罕见但非常重大的事件中恢复。 这些事件包括非暂时性的大规模故障，如影响整个区域的服务中断。 灾难恢复包括数据备份和存档，并且可能包括手动干预，如通过备份还原数据库。

如果出现可能导致大规模中断的重大灾难，Azure 平台内置的本地故障保护功能可能无法完全保护 VM/磁盘。 这包括数据中心遭遇飓风、地震、火灾或大规模硬件单元故障等灾难性事件。 此外，可能还会遇到应用程序或数据问题导致的故障。

若要保护 IaaS 工作负荷不受中断影响，应计划冗余和备份，以便能够进行恢复。 对于灾难恢复，应该在远离主站点的不同地理位置备份。 这有助于确保最初影响 VM 或磁盘的相同事件不会对备份造成影响。 有关详细信息，请参阅 [Azure 应用程序的灾难恢复](/azure/architecture/resiliency/disaster-recovery-azure-applications)。

DR 注意事项可能包括以下方面：

- 高可用性：应用程序能够以正常状态继续运行，而没有显著增加故障时间。 所谓的“正常状态”是指，应用程序有响应，用户可以连接到应用程序，并与之交互。 某些任务关键型应用程序和数据库可能需要始终可用，即使平台上有故障，也不例外。 对于这些工作负荷，可能需要为应用程序和数据计划冗余。

- 数据持续性：在某些情况下，主要注意事项是确保在灾难发生时保留数据。 因此，可能需要在不同站点中备份数据。 对于此类工作负荷，可能不需要为应用程序计划完全冗余，只需定期备份磁盘即可。

## <a name="backup-and-dr-scenarios"></a>备份和 DR 方案

让我们来看几个典型的应用程序工作负荷方案示例，以及规划灾难恢复时的注意事项。

### <a name="scenario-1-major-database-solutions"></a>方案 1：主要数据库解决方案

假设为 SQL Server 或 Oracle 等生产数据库服务器，可以支持高可用性。 关键生产应用程序和用户依赖此类数据库。 此系统的灾难恢复计划可能需要满足以下要求：

- 数据必须受保护且可恢复。
- 服务器必须可用。

灾难恢复计划可能需要将不同区域中的数据库副本作为备份进行维护。 解决方案包括主动-主动或主动-被动副本站点、定期脱机备份数据，具体视服务器可用性和数据恢复要求而定。 SQL Server 和 Oracle 等关系型数据库提供各种复制选项。 对于 SQL Server，可以使用 [SQL Server AlwaysOn 可用性组](https://msdn.microsoft.com/library/hh510230.aspx)实现高可用性。

为了实现冗余，MongoDB 等 NoSQL 数据库也支持[副本](https://docs.mongodb.com/manual/replication/)。 可以使用实现高可用性的副本。

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>方案 2：冗余 VM 群集

假设由提供冗余和负载均衡的 VM 群集处理工作负载。 例如，在区域中部署的 Cassandra 群集。 此类体系结构已在相应区域提供了高水平冗余。 不过，为了保护工作负荷免受区域级故障影响，应考虑在两个区域分布群集，或定期备份到另一个区域。

### <a name="scenario-3-iaas-application-workload"></a>方案 3：IaaS 应用程序工作负荷

让我们探讨一下 IaaS 应用程序工作负荷。 例如，这可能是 Azure VM 上运行的典型生产工作负荷。 它可能是保存内容和其他站点资源的 Web 服务器或文件服务器。 也可能是在 VM 上运行的专门定制的商业应用程序，将数据、资源和应用程序状态存储到 VM 磁盘上。 在这种情况下，请务必定期进行备份。 应根据 VM 工作负载的性质确定备份频率。 例如，如果应用程序每天都运行，并且修改数据，那么应每小时备份一次。

再例如，报表服务器从其他数据源拉取数据，并生成聚合报表。 如果丢失此 VM 或磁盘，可能导致报表丢失。 不过，可以重新运行报表进程，并重新生成输出。 在这种情况下，即使报表服务器遭遇灾难，也不会真正丢失数据。 因此，可以有高水平的容错，允许报表服务器上丢失部分数据。 在这种情况下，不太频繁地进行备份可以降低成本。

### <a name="scenario-4-iaas-application-data-issues"></a>方案 4：IaaS 应用程序数据问题

IaaS 应用程序数据问题是另一种可能的情况。 假设有一个应用程序，用于计算、维护和提供关键商业数据（如定价信息）。 新版应用程序有一个软件 bug，不仅错误地计算了定价，还破坏了平台提供的现有商业数据。 在这种情况下，最好还原到旧版应用程序和数据。 若要能够进行还原，请定期备份系统。

## <a name="disaster-recovery-solution-azure-backup"></a>灾难恢复解决方案：Azure 备份 

[Azure 备份服务](https://azure.microsoft.com/services/backup/)用于备份和 DR，适用于[托管磁盘](../articles/virtual-machines/windows/managed-disks-overview.md)和[非托管磁盘](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks)。 可以创建备份作业，其中包含基于时间的备份、VM 轻松还原和备份保留策略。 

如果将[高级存储磁盘](../articles/virtual-machines/windows/premium-storage.md)、[托管磁盘](../articles/virtual-machines/windows/managed-disks-overview.md)或其他类型磁盘与[本地冗余存储](../articles/storage/common/storage-redundancy.md#locally-redundant-storage)选项结合使用，请务必创建定期 DR 备份。 Azure 备份将数据存储到恢复服务保管库中，以供长期保留。 对备份恢复服务保管库选择[异地冗余存储](../articles/storage/common/storage-redundancy.md#geo-redundant-storage)选项。 该选项可确保将备份复制到其他 Azure 区域，以免受到区域灾难影响。

对于[非托管磁盘](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks)，可将本地冗余存储类型用于 IaaS 磁盘，但要确保为 Azure 备份恢复服务保管库启用异地冗余存储选项。

> [!NOTE]
> 如果将[异地冗余存储](../articles/storage/common/storage-redundancy.md#geo-redundant-storage)或[读取访问权限异地冗余存储](../articles/storage/common/storage-redundancy.md#read-access-geo-redundant-storage)选项用于非托管磁盘，仍需要为备份和 DR 生成一致性快照。 使用 [Azure 备份](https://azure.microsoft.com/services/backup/)或[一致性快照](#alternative-solution-consistent-snapshots)。

 下表汇总了可用于 DR 的解决方案。

| 方案 | 自动复制 | DR 解决方案 |
| --- | --- | --- |
| 高级存储磁盘 | 本地（[本地冗余存储](../articles/storage/common/storage-redundancy.md#locally-redundant-storage)） | [Azure 备份](https://azure.microsoft.com/services/backup/) |
| 托管磁盘 | 本地（[本地冗余存储](../articles/storage/common/storage-redundancy.md#locally-redundant-storage)） | [Azure 备份](https://azure.microsoft.com/services/backup/) |
| 非托管本地冗余存储磁盘 | 本地（[本地冗余存储](../articles/storage/common/storage-redundancy.md#locally-redundant-storage)） | [Azure 备份](https://azure.microsoft.com/services/backup/) |
| 非托管异地冗余存储磁盘 | 跨区域（[异地冗余存储](../articles/storage/common/storage-redundancy.md#geo-redundant-storage)） | [Azure 备份](https://azure.microsoft.com/services/backup/)<br/>[一致性快照](#alternative-solution-consistent-snapshots) |
| 非托管读取访问权限异地冗余存储磁盘 | 跨区域（[读取访问权限异地冗余存储](../articles/storage/common/storage-redundancy.md#read-access-geo-redundant-storage)） | [Azure 备份](https://azure.microsoft.com/services/backup/)<br/>[一致性快照](#alternative-solution-consistent-snapshots) |

在可用性集和 Azure 备份中使用托管磁盘是实现高可用性的最佳方式。 如果使用非托管磁盘，仍可以使用 Azure 备份进行 DR。 如果无法使用 Azure 备份，请采用后面部分所述的[一致性快照](#alternative-solution-consistent-snapshots)，作为备用的备份和 DR 解决方案。

下面展示了在应用程序或基础结构一级可选择的高可用性、备份和 DR 选项：

| 级别 |   高可用性   | 备份或 DR |
| --- | --- | --- |
| Application | SQL Server AlwaysOn | Azure 备份 |
| 基础结构    | 可用性集  | 具有一致快照的异地冗余存储 |

### <a name="using-azure-backup"></a>使用 Azure 备份 

[Azure 备份](../articles/backup/backup-azure-vms-introduction.md)可将运行 Windows 或 Linux 的 VM 备份到 Azure 恢复服务保管库中。 必须在生成数据的应用程序仍在运行时备份业务关键型数据，这让备份和还原业务关键型数据变得更加复杂。 

为了解决此问题，Azure 备份为 Microsoft 工作负荷提供应用程序一致性备份。 它使用卷影服务确保将数据正确写入存储中。 对于 Linux VM，只能进行文件一致性备份，因为 Linux 没有与卷影服务相当的功能。

![Azure 备份流][1]

在原定时间启动备份作业时，Azure 备份会触发在 VM 中安装的备份扩展，以生成时间点快照。 创建快照时，会借助卷影服务来获取虚拟机中磁盘的一致性快照，不必关闭该虚拟机。 生成所有磁盘的一致性快照前，VM 中的备份扩展会刷新所有写入。 生成快照后，数据由 Azure 备份传输到备份保管库中。 为了使备份过程更加高效，服务只标识并传输在上次备份后已更改的数据块。

若要还原，可以通过 Azure 备份查看可用备份，再启动还原。 可以通过 [Azure 门户](https://portal.azure.com/)、[PowerShell](../articles/backup/backup-azure-vms-automation.md) 或 [Azure CLI](/cli/azure/) 创建和还原 Azure 备份。 

### <a name="steps-to-enable-a-backup"></a>备份启用步骤

执行以下步骤可以使用 [Azure 门户](https://portal.azure.com/)启用 VM 备份。 步骤可能有一些差异，具体视确切方案而定。 如需了解完整详情，请参阅 [Azure 备份](../articles/backup/backup-azure-vms-introduction.md)一文。 Azure 备份还[支持使用托管磁盘的 VM](https://azure.microsoft.com/blog/azure-managed-disk-backup/)。

1.  为 VM 创建恢复服务保管库：

    a. 在 [Azure 门户](https://portal.azure.com/)中，浏览到“所有资源”并找到“恢复服务保管库”。

    b. 在“恢复服务保管库”菜单上，单击“添加”，并按相关步骤操作，在 VM 所在区域中新建一个保管库。 例如，如果 VM 位于美国西部区域，请为保管库选择“美国西部”。

2.  验证新建保管库的存储复制功能。 访问“恢复服务保管库”下的保管库，转到“设置” > “备份配置”。 确保“异地冗余存储”选项默认处于选中状态。 这可确保保管库自动复制到辅助数据中心。 例如，位于美国西部的保管库会自动复制到美国东部。

3.  配置备份策略，再从同一 UI 中选择 VM。

4.  确保在 VM 上安装了备份代理。 如果 VM 是使用 Azure 库映像创建而成，表明备份代理已安装。 否则（即使用的是自定义映像），请根据相关说明[在虚拟机中安装 VM 代理](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent-on-the-virtual-machine)。

5.  确保 VM 允许备份服务的网络连接功能正常运行。 遵循[网络连接](../articles/backup/backup-azure-arm-vms-prepare.md#establish-network-connectivity)的说明。

6.  完成上述步骤后，会按备份策略中指定的时间间隔定期进行备份。 如有必要，可以在 Azure 门户的保管库仪表板中手动触发首个备份。

若要了解如何使用脚本自动执行 Azure 备份，请参阅[用于备份 VM 的 PowerShell cmdlet](../articles/backup/backup-azure-vms-automation.md)。

### <a name="steps-for-recovery"></a>恢复步骤

如果需要修复或重新生成 VM，可以从保存库中的任意备份恢复点还原 VM。 可以通过下列两种不同方式来执行恢复：

-   可以新建 VM，表示处于特定时间点的已备份 VM。

-   可以还原磁盘，再使用 VM 模板自定义和重新生成还原后的 VM。 

有关详细信息，请参阅[使用 Azure 门户还原虚拟机](../articles/backup/backup-azure-arm-restore-vms.md)的说明。 这篇文档还逐步介绍了在主数据中心发生灾难时，如何使用异地冗余备份保管库将已备份 VM 还原到已配对数据中心。 在这种情况下，Azure 备份使用次要区域中的计算服务来创建还原后的虚拟机。

也可以使用 PowerShell [还原 VM](../articles/backup/backup-azure-arm-restore-vms.md#restore-a-vm-during-an-azure-datacenter-disaster) 或[通过还原后的磁盘新建 VM](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。

## <a name="alternative-solution-consistent-snapshots"></a>备用解决方案：一致性快照

如果无法使用 Azure 备份，可以使用快照实现自己的备份机制。 为 VM 使用的所有磁盘创建一致性快照，再将这些快照复制到另一个区域的过程比较复杂。 因此，Azure 认为相对于生成自定义解决方案，使用备份服务是更好的选择。 

如果将读取访问权限异地冗余存储/异地冗余存储用于磁盘，快照会自动复制到辅助数据中心。 如果将本地冗余存储用于磁盘，需要自行复制数据。 有关详细信息，请参阅[使用增量快照备份 Azure 非托管 VM 磁盘](../articles/virtual-machines/windows/incremental-snapshots.md)。

快照表示处于特定时间点的对象。 快照因保留数据的大小递增而产生费用。 有关详细信息，请参阅[创建 blob 快照](../articles/storage/blobs/storage-blob-snapshots.md)。

### <a name="create-snapshots-while-the-vm-is-running"></a>当 VM 正在运行时创建快照

尽管可以随时生成快照，但如果 VM 正在运行，就仍有数据流式传输到磁盘上，快照可能包含部分正在执行的操作。 此外，如果涉及到多个磁盘，各个磁盘的快照可能在不同时间生成。 也就是说，这些快照可能不协调。 对于带分区的卷（如果在备份期间发生了更改，文件就会被破坏），这产生的问题尤其多。

为了避免这种情况发生，必须在备份过程中执行以下步骤：

1.  冻结所有磁盘。

2.  刷新所有挂起写入。

3.  为所有磁盘[创建 Blob 快照](../articles/storage/blobs/storage-blob-snapshots.md)。

某些 Windows 应用程序（如 SQL Server）通过卷影服务提供协调的备份机制，以创建应用程序一致性备份。 在 Linux 上，可以使用 fsfreeze 等工具来协调磁盘。 此工具提供文件一致性备份，而不是应用程序一致性快照。 此过程比较复杂。因此，应考虑使用 [Azure 备份](../articles/backup/backup-azure-vms-introduction.md)或已实施此过程的第三方备份解决方案。

上述过程会生成所有 VM 磁盘的协调快照集合，用于表示处于特定时间点的 VM。 这就是 VM 的备份还原点。 可以按原定时间间隔重复执行此过程，从而创建定期备份。 请参阅[将备份复制到另一个区域](#copy-the-snapshots-to-another-region)，了解将快照复制到另一个区域进行 DR 的步骤。

### <a name="create-snapshots-while-the-vm-is-offline"></a>当 VM 脱机时创建快照

创建一致性备份的另一种做法是，关闭 VM，再为每个磁盘生成 Blob 快照。 生成 Blob 快照比协调正在运行的 VM 的快照更为简单，但会出现几分钟的停机时间。

1. 关闭 VM。

2. 创建每个虚拟硬盘 Blob 的快照，这只需要几秒钟的时间。

    若要创建快照，可以使用 [PowerShell](../articles/storage/common/storage-powershell-guide-full.md)、[Azure 存储 REST API](https://msdn.microsoft.com/library/azure/ee691971.aspx)、[Azure CLI](/cli/azure/) 或 Azure 存储客户端库之一（如[用于 .NET 的存储客户端库](https://msdn.microsoft.com/library/azure/hh488361.aspx)）。

3. 启动 VM，这将终止故障时间。 整个过程通常会在几分钟内完成。

此过程生成所有磁盘的一组一致性快照，提供 VM 的备份还原点。

### <a name="copy-the-snapshots-to-another-region"></a>将快照复制到另一个区域

仅仅创建快照可能不足以执行 DR。 还必须将快照备份复制到另一个区域。

如果将异地冗余存储或读取访问权限异地冗余存储用于磁盘，快照会自动复制到次要区域。 复制前，可能会有几分钟的延迟。 如果在快照完成复制之前主数据中心出现故障，则无法从辅助数据中心访问快照。 这种情况发生的可能性很小。

> [!NOTE] 
> 仅在异地冗余存储或读取访问权限异地冗余存储帐户中使用磁盘无法保护 VM 免受灾难影响。 还必须创建协调快照，或使用 Azure 备份。 若要将 VM 恢复到一致状态，必须这样做。

如果使用本地冗余存储，必须在创建快照后立即将快照复制到其他存储帐户中。 复制目标可以是其他区域中的本地冗余存储帐户，这样便能将副本复制到远程区域中。 也可将快照复制到同一区域中的读取访问权限异地冗余存储帐户。 在这种情况下，快照会延迟复制到远程次要区域。 在复制完成后，备份就不会受到主站点所发生灾难的影响。

若要有效复制 DR 增量快照，请参阅[使用增量快照备份 Azure 非托管 VM 磁盘](../articles/virtual-machines/windows/incremental-snapshots.md)中的说明。

![使用递增快照备份 Azure 非托管 VM 磁盘][2]

### <a name="recovery-from-snapshots"></a>通过快照恢复

若要检索快照，请进行复制，以新建 blob。 若要从主帐户复制快照，可将快照复制到快照的基 Blob。 此过程会将磁盘还原到快照。 此过程称为“提升快照”。 若要从辅助帐户复制快照备份（使用读取访问权限异地冗余存储帐户时），必须将快照复制到主帐户。 可以[使用 PowerShell](../articles/storage/common/storage-powershell-guide-full.md) 或 AzCopy 实用工具复制快照。 有关详细信息，请参阅[使用 AzCopy 命令行实用工具传输数据](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)。

对于包含多个磁盘的 VM，必须复制属于同一协调还原点的所有快照。 将快照复制到可写 VHD Blob 后，可以通过 Blob 使用 VM 模板重新创建 VM。

## <a name="other-options"></a>其他选项

### <a name="sql-server"></a>SQL Server

在 VM 中运行的 SQL Server 有自己的内置功能，可将 SQL Server 数据库备份到 Azure Blob 存储或文件共享。 如果存储帐户是异地冗余存储或读取访问权限异地冗余存储，那么在发生灾难时，可以在存储帐户的辅助数据中心内访问这些备份，需要遵循前面所述的相同限制。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的备份和还原](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md)。 除了备份和还原外，[SQL Server AlwaysOn 可用性组](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md)还可以维护数据库的次要副本。 这可以大大减少灾难恢复时间。

## <a name="other-considerations"></a>其他注意事项

本文介绍了如何通过备份或生成 VM 及其磁盘的快照来支持灾难恢复，以及如何使用这些快照恢复数据。 通过 Azure 资源管理器模型，许多人都可以使用模板在 Azure 中创建虚拟机和其他基础结构。 可以使用模板创建每次配置都相同的 VM。 如果使用自定义映像创建 VM，还必须务必使用读取访问权限异地冗余存储帐户存储这些映像，从而保护它们。

因此，备份过程可能分为以下两部分：

- 备份数据（磁盘）。
- 备份配置（模板和自定义映像）。

可能需要备份数据和配置，或备份服务可能会处理所有这些事务，具体视选择的备份选项而定。

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>附录：了解数据冗余的影响

对于 Azure 中的存储帐户，应就灾难恢复考虑三类数据冗余：本地冗余、异地冗余或提供读取访问权限的异地冗余。 

本地冗余存储在同一数据中心内保留三个数据副本。 VM 写入数据时，所有三个副本都在向调用方返回成功结果之前进行更新，因此可以知道它们是完全相同的。 磁盘不受本地故障影响，因为这三个副本同时全都受到影响的可能性极低。 使用本地冗余存储时不存在异地冗余，因此影响整个数据中心或存储单元的灾难性故障可能会对磁盘造成影响。

使用异地冗余存储和读取访问权限异地冗余存储时，三个数据副本都保留在自行选择的主要区域。 另外三个数据副本保留在 Azure 设置的相应次要区域。 例如，如果将数据存储在美国西部，则数据会复制到美国东部。 副本保留是以异步方式完成的，主要站点和辅助站点的更新稍有延迟。 辅助站点上的磁盘副本具有每磁盘一致性（有延迟），但多个活动磁盘的副本可能不会彼此同步。 若要跨多个磁盘拥有一致副本，需要创建一致性快照。

异地冗余存储和读取访问权限异地冗余存储 的主要区别在于，使用读取访问权限异地冗余存储可以随时读取辅助副本。 如果出现问题，导致主要区域中的数据无法访问，那么 Azure 团队会竭尽全力恢复访问。 尽管主要区域出现故障，但如果启用了读取访问权限异地冗余存储，便可以在辅助数据中心访问数据。 因此，如果计划在主要区域无法访问时读取副本，应考虑使用读取访问权限异地冗余存储。

如果演变成严重故障，Azure 团队可能会触发异地故障转移，并将主 DNS 条目更改为指向辅助存储。 此时，如果启用了异地冗余存储或读取访问权限异地冗余存储，便可以在之前为次要区域的区域中访问数据。 也就是说，如果存储帐户是异地冗余存储且出现故障，那么只有在进行异地故障转移后，才能访问辅助存储。

有关详细信息，请参阅[在 Azure 存储中断时该怎么办](../articles/storage/common/storage-disaster-recovery-guidance.md)。 

>[!NOTE] 
>Microsoft 控制是否进行故障转移。 故障转移无法由存储帐户控制，因此并非由个人客户决定。 若要为特定存储帐户或虚拟机磁盘实现灾难恢复，必须使用本文前面所述的方法。



[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png

---
title: 体系结构概述
description: 概述 Azure 备份服务使用的体系结构、组件和流程。
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b093c6702bb26fe537622727fe1b623141bf4160
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273613"
---
# <a name="azure-backup-architecture-and-components"></a>Azure 备份体系结构和组件

可以使用[Azure 备份服务](backup-overview.md)将数据备份到 Microsoft Azure 云平台。 本文汇总了 Azure 备份体系结构、组件和流程。

## <a name="what-does-azure-backup-do"></a>Azure 备份有什么功能？

Azure 备份备份在本地计算机和 Azure 虚拟机（VM）实例上运行的数据、计算机状态和工作负荷。 有许多 Azure 备份方案。

## <a name="how-does-azure-backup-work"></a>Azure 备份的工作原理是什么？

您可以使用多种方法来备份计算机和数据：

- **备份本地计算机**：
  - 可以使用 Azure 备份 Microsoft Azure 恢复服务（MARS）代理将本地 Windows 计算机直接备份到 Azure。 不支持 Linux 计算机。
  - 可以将本地计算机备份到备份服务器-系统中心 Data Protection Manager （DPM）或 Microsoft Azure 备份服务器（MABS）。 然后，可以将备份服务器备份到 Azure 中的恢复服务保管库。

- **备份 Azure VM**：
  - 可以直接备份 Azure VM。 Azure 备份会将备份扩展安装到 VM 上运行的 Azure VM 代理。 此扩展备份整个 VM。
  - 可以通过运行 MARS 代理来备份 Azure VM 上的特定文件和文件夹。
  - 可以将 Azure Vm 备份到在 Azure 中运行的 MABS，然后将 MABS 备份到恢复服务保管库。

详细了解[可备份的内容](backup-overview.md)和有关支持的[备份方案](backup-support-matrix.md)。

## <a name="where-is-data-backed-up"></a>数据备份到何处？

Azure 备份将备份的数据存储在恢复服务保管库中。 保管库是 Azure 中用于保存数据的联机存储实体，如备份副本、恢复点和备份策略。

恢复服务保管库具有以下功能：

- 使用保管库可以方便地组织备份数据，并将管理开销降至最低。
- 在每个 Azure 订阅中，最多可以创建 500 个保管库。
- 可以监视保管库中的已备份项，包括 Azure Vm 和本地计算机。
- 可以使用 Azure [基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 来管理对保管库的访问。
- 指定如何复制保管库中的数据以实现冗余：
  - **本地冗余存储（LRS）** ：若要防止数据中心出现故障，可以使用 LRS。 LRS 将数据复制到存储缩放单元。 [了解详细信息](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)。
  - **异地冗余存储（GRS）** ：若要防止发生区域范围的故障，可以使用 GRS。 GRS 将数据复制到次要区域。 [了解详细信息](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。
  - 默认情况下，恢复服务保管库使用 GRS。

## <a name="backup-agents"></a>备份代理

Azure 备份提供不同的备份代理，具体取决于要备份的计算机类型：

**代理** | **详细信息**
--- | ---
**MARS 代理** | <ul><li>在单独的本地 Windows Server 计算机上运行，以备份文件、文件夹和系统状态。</li> <li>在 Azure Vm 上运行，以便备份文件、文件夹和系统状态。</li> <li>在 DPM/MABS 服务器上运行，将 DPM/MABS 本地存储磁盘备份到 Azure。</li></ul>
**Azure VM 扩展** | 在 Azure VM 上运行，可以将其备份到保管库。

## <a name="backup-types"></a>备份类型

下表说明了不同类型的备份及其使用时间：

**备份类型** | **详细信息** | **使用情况**
--- | --- | ---
**完整** | 完整备份包含整个数据源。 比差异备份或增量备份占用更多的网络带宽。 | 用于初始备份。
**差异** |  差异备份存储自初始完整备份后更改的块。 使用的网络和存储量更少，并且不保留未更改数据的冗余副本。<br/><br/> 低效，因为会传输和存储在以后的备份之间保持不变的数据块。 | Azure 备份不使用此备份类型。
**增量** | 增量备份仅存储自上一次备份以来发生更改的数据块。 存储和网络效率高。 <br/><br/> 通过增量备份，无需通过完整备份进行补充。 | DPM/MABS 使用此备份类型来备份磁盘，备份到 Azure 的所有方案都使用此备份类型。 不用于 SQL Server 备份。

## <a name="sql-server-backup-types"></a>SQL Server 备份类型

下表说明了用于 SQL Server 数据库的不同类型的备份，以及它们的使用频率：

**备份类型** | **详细信息** | **使用情况**
--- | --- | ---
**完整备份** | 完整数据库备份将备份整个数据库。 它包含特定数据库或一组文件组或文件中的所有数据。 完整备份还包含足够的日志来恢复这些数据。 | 每天最多可以触发一次完整备份。<br/><br/> 你可以选择在每天或每周间隔进行完整备份。
**差异备份** | 差异备份基于最近一次的完整数据备份。<br/><br/> 它只捕获自完整备份以来发生更改的数据。 |  每天最多可以触发一次差异备份。<br/><br/> 不能在同一天配置完整备份和差异备份。
**事务日志备份** | 日志备份可以实现时间点还原，精度可达到特定的秒。 | 最多可以配置为每隔 15 分钟创建事务日志备份。

### <a name="comparison-of-backup-types"></a>备份类型的比较

存储消耗、恢复时间目标 (RTO) 和网络消耗因每种备份类型而异。 下图显示了备份类型的比较：

- 数据源 A 由10个存储块（A1-A10，每月备份）组成。
- 第一个月，存储块 A2、A3、A4 和 A9 变化，第二个月，存储块 A5 变化。
- 对于差异备份，在第二个月，将备份已更改的块 A2、A3、A4 和 A9。 第三个月，会再次备份这些相同的存储块，以及已更改的存储块 A5。 下次进行完整备份之前，将继续对已更改的存储块进行备份。
- 对于增量备份，在第二个月内，块 A2、A3、A4 和 A9 标记为已更改和已传输。 在第三个月，仅标记已更改的存储块 A5，并进行传输。

![显示备份方法比较的图像](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>备份功能

下表总结了不同类型的备份支持的功能：

**功能** | **直接备份文件和文件夹（使用 MARS Agent）** | **Azure VM 备份** | **具有 DPM/MABS 的计算机或应用**
--- | --- | --- | ---
备份到保管库 | ![是][green] | ![是][green] | ![是][green]
备份到 DPM/MABS 磁盘，然后备份到 Azure | | | ![是][green]
压缩发送的备份数据 | ![是][green] | 传输数据时不使用压缩。 存储消耗略有提高，但还原速度更快。  | ![是][green]
运行增量备份 |![是][green] |![是][green] |![是][green]
备份已删除重复数据的磁盘 | | | ![部分][yellow]<br/><br/> 仅适用于本地部署的 DPM/MABS 服务器。

![表键](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>备份策略概要

- 备份策略是按保管库创建的。
- 可为以下工作负荷的备份创建备份策略
  - Azure VM
  - Azure VM 中的 SQL
  - Azure 文件共享
- 可将一个策略分配到多个资源。 可以使用一个 Azure VM 备份策略来保护多个 Azure VM。
- 策略由两个部分组成
  - 计划：何时创建备份
  - 保留期：每个备份应保留多长时间。
- 可将计划定义为带有特定时间点的“每日”或“每周”计划。
- 可以针对“每日”、“每周”、“每月”、“每年”备份点定义保留期。
- “每周”是指在特定的星期日期进行备份，“每月”是指在特定的月份日期进行备份，“每年”是指在特定的年份日期进行备份。
- “每月”、“每年”备份点的保留期称为“LongTermRetention”。
- 创建保管库后，还会创建名为 "DefaultPolicy" 的 Azure VM 备份的策略，并将其用于备份 Azure Vm。

## <a name="architecture-built-in-azure-vm-backup"></a>体系结构：内置 Azure VM 备份

1. 为 Azure VM 启用备份时，会根据指定的计划运行备份。
1. 在第一次备份过程中，如果 VM 正在运行，则将在 VM 上安装备份扩展。
    - 对于 Windows Vm，安装了 VMSnapshot 扩展。
    - 对于 Linux Vm，已安装 VMSnapshot Linux 扩展。
1. 此扩展采用存储级快照。
    - 对于正在运行的 Windows Vm，备份与 Windows 卷影复制服务（VSS）进行协调，以获取 VM 的应用一致性快照。 默认情况下，备份会进行完整的 VSS 备份。 如果 Azure 备份无法创建应用一致性快照，则会创建文件一致性快照。
    - 对于 Linux Vm，备份将使用文件一致的快照。 对于应用一致的快照，需要手动自定义预/后脚本。
    - 可以通过并行备份每个 VM 磁盘来优化备份。 对于每个要备份的磁盘，Azure 备份将读取磁盘上的块，并只存储已更改的数据。
1. 创建快照后，数据将传输到保管库。
    - 仅复制上次备份后更改的数据块。
    - 不会加密数据。 Azure 备份可备份使用 Azure 磁盘加密进行加密的 Azure Vm。
    - 快照数据可能不会立即复制到保管库。 在高峰时间，备份可能需要几个小时。 对于每日备份策略，VM 的总备份时间将小于24小时。
1. 将数据发送到保管库后，将创建恢复点。 默认情况下，快照在删除前两天内保留。 此功能允许从这些快照还原操作，从而缩短还原时间。 它减少了从保管库转换和复制数据所需的时间。 请参阅[Azure 备份即时还原功能](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability)。

无需显式允许 internet 连接来备份 Azure Vm。

![备份 Azure VM](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>体系结构：直接备份本地 Windows Server 计算机或 Azure VM 文件或文件夹

1. 若要设置此方案，请在计算机上下载并安装 MARS 代理。 然后，你可以选择要备份的内容、运行备份的时间，以及在 Azure 中保留多长时间。
1. 初始备份根据你的备份设置运行。
1. MARS 代理使用 VSS 来获取选定用于备份的卷的时间点快照。
    - MARS 代理仅使用 Windows 系统写入操作来捕获快照。
    - 由于代理不使用任何应用程序 VSS 编写器，因此它不会捕获应用一致性快照。
1. 使用 VSS 拍摄快照后，MARS 代理会在配置备份时指定的缓存文件夹中创建虚拟硬盘（VHD）。 代理还存储每个数据块的校验和。
1. 增量备份会根据指定的计划运行，除非你运行按需备份。
1. 在增量备份中，将会标识已更改的文件，并创建新的 VHD。 VHD 经过压缩和加密，然后发送到保管库。
1. 增量备份完成后，新的 VHD 合并为在初始复制后创建的 VHD。 此合并 VHD 提供要用于进行比较的最新状态，以便进行正在进行的备份。

![通过 MARS 代理备份本地 Windows Server 计算机](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>体系结构：备份到 DPM/MABS

1. 在要保护的计算机上安装 DPM 或 MABS 保护代理。 然后，将计算机添加到 DPM 保护组。
    - 若要保护本地计算机，DPM 或 MABS 服务器必须位于本地。
    - 若要保护 Azure VM，MABS 服务器必须位于 Azure 中，并作为 Azure VM 运行。
    - 利用 DPM/MABS，你可以保护备份卷、共享、文件和文件夹。 您还可以保护计算机的系统状态（裸机），并使用应用程序感知备份设置来保护特定的应用程序。
1. 在 DPM/MABS 中为计算机或应用设置保护时，可以选择备份到 MABS/DPM 本地磁盘以进行短期存储，并选择备份到 Azure 以实现在线保护。 你还可以指定备份到本地 DPM/MABS 存储的时间，以及何时运行到 Azure 的联机备份。
1. 受保护工作负荷的磁盘会根据指定的计划备份到本地 MABS/DPM 磁盘。
1. Dpm/MABS 磁盘由 DPM/MABS 服务器上运行的 MARS 代理备份到保管库。

![备份受 DPM 或 MABS 保护的计算机和工作负荷](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM 存储

Azure VM 使用磁盘来存储其操作系统、应用和数据。 每个 Azure VM 至少有两个磁盘：一个用于操作系统的磁盘和一个临时磁盘。 Azure Vm 还可以有用于应用数据的数据磁盘。 磁盘以 VHD 的形式进行存储。

- Vhd 以页 blob 的形式存储在 Azure 中的标准存储帐户或高级存储帐户中：
  - **标准存储：** 对运行工作负荷不受延迟影响的 Vm 提供可靠、低成本的磁盘支持。 标准存储可以使用标准固态硬盘（SSD）磁盘或标准硬盘驱动器（HDD）磁盘。
  - **高级存储：** 高性能磁盘支持。 使用高级·SSD 磁盘。
- 磁盘具有不同的性能层：
  - **标准 HDD 磁盘：** 由 Hdd 提供支持，并用于经济高效的存储。
  - **标准 SSD 磁盘：** 组合高级 SSD 磁盘和标准 HDD 磁盘的元素。 提供比 HDD 更一致的性能和可靠性，但仍是经济高效的。
  - **高级 SSD 磁盘：** 由 Ssd 提供支持，并为运行 i/o 密集型工作负荷的 Vm 提供高性能和低延迟。
- 磁盘可以是托管磁盘或非托管磁盘：
  - **非托管磁盘：** Vm 使用的传统类型的磁盘。 对于这些磁盘，可以创建自己的存储帐户，并在创建磁盘时指定该存储帐户。 然后，需要确定如何最大程度地提高 Vm 的存储资源。
  - **托管磁盘：** Azure 会为你创建和管理存储帐户。 指定磁盘大小和性能层，Azure 将为你创建托管磁盘。 添加磁盘和缩放 Vm 时，Azure 会处理存储帐户。

有关适用于 Vm 的磁盘存储和可用磁盘类型的详细信息，请参阅以下文章：

- [适用于 Windows Vm 的 Azure 托管磁盘](../virtual-machines/windows/managed-disks-overview.md)
- [适用于 Linux Vm 的 Azure 托管磁盘](../virtual-machines/linux/managed-disks-overview.md)
- [Vm 的可用磁盘类型](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>备份和还原包含高级存储的 Azure Vm

可以通过 Azure 备份使用高级存储来备份 Azure Vm：

- 在使用高级存储备份 Vm 的过程中，备份服务会在存储帐户中创建一个名为*AzureBackup*的临时暂存位置。 暂存位置的大小等于恢复点快照的大小。
- 确保高级存储帐户有足够的可用空间，可以容纳临时暂存位置。 有关详细信息，请参阅[高级页 blob 存储帐户的可伸缩性目标](../storage/blobs/scalability-targets-premium-page-blobs.md)。 不要修改暂存位置。
- 备份作业完成后，将删除暂存位置。
- 用于暂存位置的存储的价格与[高级存储定价](../virtual-machines/windows/disks-types.md#billing)相一致。

使用高级存储还原 Azure Vm 时，可以将其还原到高级或标准存储。 通常，会将其还原到高级存储。 但是，如果你只需要 VM 中的一部分文件，则将它们还原到标准存储可能是经济高效的。

### <a name="back-up-and-restore-managed-disks"></a>备份和还原托管磁盘

可以通过托管磁盘备份 Azure Vm：

- 像备份其他任何 Azure VM 一样备份使用托管磁盘的 VM。 可以直接从虚拟机设置备份 VM，或者，可以在恢复服务保管库中为 VM 启用备份。
- 通过基于托管磁盘的 RestorePoint 收集，可以在托管磁盘上备份 VM。
- Azure 备份还支持使用使用 Azure 磁盘加密进行加密的托管磁盘备份 Vm。

使用托管磁盘还原 Vm 时，可以使用托管磁盘或存储帐户还原到完整的 VM：

- 在还原过程中，Azure 会处理托管磁盘。 如果使用的是存储帐户选项，则可以管理在还原过程中创建的存储帐户。
- 如果还原已加密的托管 VM，请在启动还原过程之前确保 VM 的密钥和机密存在于密钥保管库中。

## <a name="next-steps"></a>后续步骤

- 查看支持矩阵，[了解备份方案支持的功能和限制](backup-support-matrix.md)。
- 为以下方案之一设置备份：
  - [备份 Azure vm](backup-azure-arm-vms-prepare.md)。
  - 不使用备份服务器[直接备份 Windows 计算机](tutorial-backup-windows-server-to-azure.md)。
  - [设置 MABS](backup-azure-microsoft-azure-backup.md) 以备份到 Azure，然后将工作负荷备份到 MABS。
  - [设置 DPM](backup-azure-dpm-introduction.md) 以备份到 Azure，然后将工作负荷备份到 DPM。

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

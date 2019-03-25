---
title: Azure 备份体系结构
description: 概述 Azure 备份服务使用的体系结构、组件和流程。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 98ffe145103b4be04014627ed04d04dcf7542015
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368946"
---
# <a name="azure-backup-architecture"></a>Azure 备份体系结构

可以使用[Azure 备份服务](backup-overview.md)将数据备份到 Microsoft Azure 云平台。 本文汇总了 Azure 备份体系结构、组件和流程。 

## <a name="what-does-azure-backup-do"></a>Azure 备份有什么功能？

Azure 备份备份数据、 计算机的状态，并在本地计算机上和 Azure 虚拟机 (VM) 实例上运行的工作负荷。 有许多 Azure 备份方案。

## <a name="how-does-azure-backup-work"></a>Azure 备份的工作原理是什么？

使用多种方法可以备份计算机和数据：

- **备份本地计算机**：
    - 你可以备份的本地 Windows 计算机直接到 Azure 使用 Azure 备份 Microsoft Azure 恢复服务 (MARS) 代理。 不支持 Linux 计算机。
    - 可以将本地计算机上备份到备份服务器 （System Center Data Protection Manager (DPM) 或 Microsoft Azure 备份服务器 (MABS)）。 您然后备份到恢复服务保管库在 Azure 中备份的服务器。

- **备份 Azure VM**：
    - 可以直接备份 Azure VM。 Azure 备份将备份扩展安装到，在 VM 上运行的 Azure VM 代理。 此扩展备份整个 VM。
    - 可以通过运行 MARS 代理来备份 Azure VM 上的特定文件和文件夹。
    - 可以备份 Azure Vm 到 Azure 中运行 MABS，你可以然后备份 MABS 到恢复服务保管库。

详细了解如何[你可以备份什么](backup-overview.md)而是有关[受支持的备份方案](backup-support-matrix.md)。

## <a name="where-is-data-backed-up"></a>数据备份到何处？

Azure 备份将备份数据存储在恢复服务保管库中。 保管库是用来保存数据，例如备份副本、 恢复点和备份策略的 Azure 中的联机存储实体。

恢复服务保管库具有以下功能：

- 使用保管库可以方便地组织备份数据，并将管理开销降至最低。
- 在每个 Azure 订阅中，最多可以创建 500 个保管库。
- 你可以监视备份保管库，包括 Azure Vm 和本地计算机中的项。
- 可以使用 Azure [基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 来管理对保管库的访问。
- 指定如何复制保管库中的数据以实现冗余：
    - **本地冗余存储 (LRS)**：若要防止在数据中心发生故障，可以使用 LRS。 LRS 将数据复制到存储缩放单元。 [了解详细信息](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)。
    - **异地冗余存储 (GRS)**：若要避免出现全区域服务中断，可以使用 GRS。 GRS 将数据复制到次要区域。 [了解详细信息](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。 
    - 默认情况下，恢复服务保管库使用 GRS。 

## <a name="backup-agents"></a>备份代理

Azure 备份提供了不同的备份代理，具体取决于哪种类型的计算机正在备份：

**代理** | **详细信息** 
--- | --- 
**MARS 代理** | <ul><li>在独立的本地 Windows Server 备份文件、 文件夹和系统状态的计算机上运行。</li> <li>在文件、 文件夹和系统状态备份 Azure Vm 上运行。</li> <li>在 DPM/MABS 服务器来将 DPM/MABS 本地存储磁盘备份到 Azure 上运行。</li></ul> 
**Azure VM 扩展** | 在 Azure VM 上运行，可以将其备份到保管库。

## <a name="backup-types"></a>备份类型

下表介绍了不同类型的备份和使用它们：

**备份类型** | **详细信息** | **使用情况**
--- | --- | ---
**完整** | 完整备份包含整个数据源。 采用比差异或增量备份的更多网络带宽。 | 用于初始备份。
**差异** |  差异备份将存储自初始完整备份以来更改的块。 使用较小的网络和存储量并不会保持不变的数据的冗余副本。<br/><br/> 因为更高版本的备份之间保持不变的数据块将被传输并存储效率低下。 | Azure 备份不使用此备份类型。
**增量** | 增量备份存储仅自上次备份以来更改的数据块。 存储和网络效率高。 <br/><br/> 使用增量备份时，没有必要来补充完整备份。 | DPM/MABS 使用此备份类型来备份磁盘，备份到 Azure 的所有方案都使用此备份类型。

## <a name="sql-server-backup-types"></a>SQL Server 备份类型

下表介绍了不同类型的备份使用的 SQL Server 数据库和使用频率：

**备份类型** | **详细信息** | **使用情况**
--- | --- | ---
**完整备份** | 完整数据库备份将备份整个数据库。 它包含在特定数据库或一组文件组或文件中的所有数据。 完整备份还包含足够的日志来恢复该数据。 | 每天最多可以触发一次完整备份。<br/><br/> 您可以选择在每日或每周的时间间隔进行一次完整备份。
**差异备份** | 差异备份基于最新、 上一条的完整数据备份。<br/><br/> 它只捕获自完整备份以来发生更改的数据。 |  每天最多可以触发一次差异备份。<br/><br/> 不能在同一天配置完整备份和差异备份。
**事务日志备份** | 日志备份可以实现时间点还原，精度可达到特定的秒。 | 最多可以配置为每隔 15 分钟创建事务日志备份。

### <a name="comparison-of-backup-types"></a>备份类型的比较

存储消耗、恢复时间目标 (RTO) 和网络消耗因每种备份类型而异。 下图显示了备份类型的比较：

- 数据源 A 由 10 存储块 A1-A10 每月备份。
- 第一个月，存储块 A2、A3、A4 和 A9 变化，第二个月，存储块 A5 变化。
- 对于差异备份，在第二个月，将备份已更改的块 A2、A3、A4 和 A9。 第三个月，会再次备份这些相同的存储块，以及已更改的存储块 A5。 下次进行完整备份之前，将继续对已更改的存储块进行备份。
- 对于增量备份，在第二个月，块 A2、 A3、 A4 和 A9 将标记为已更改，传输。 在第三个月，仅标记已更改的存储块 A5，并进行传输。 

![显示备份方法比较的图像](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>备份功能

下表总结了不同类型的备份支持的功能：

**功能** | **在本地 Windows Server 计算机 （直接）** | **Azure VM** | **计算机或使用 DPM/MABS 应用**
--- | --- | --- | ---
备份到保管库 | ![是][green] | ![是][green] | ![是][green] 
备份到 DPM/MABS 磁盘，然后到 Azure | | | ![是][green] 
压缩发送的备份数据 | ![是][green] | 传输数据时不使用压缩。 存储消耗略有提高，但还原速度更快。  | ![是][green] 
运行增量备份 |![是][green] |![是][green] |![是][green] 
备份已删除重复数据的磁盘 | | | ![部分][yellow]<br/><br/> 仅适用于本地部署的 DPM/MABS 服务器。 

![表键](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>体系结构：直接备份 Azure VM

1. 时为 Azure VM 启用备份，备份将根据您指定的计划运行。
1. 在过程中的第一个备份，备份扩展被安装在 VM 上如果 VM 正在运行。
    - 对于 Windows Vm，安装 VMSnapshot 扩展。
    - 对于 Linux Vm，安装 VMSnapshot Linux 扩展。
1. 该扩展会创建存储级快照。 
    - 对于正在运行的 Windows Vm，备份协调与 Windows 卷影复制服务 (VSS) 来创建 VM 应用一致快照。 默认情况下，备份将完整的 VSS 备份。 如果 Azure 备份无法创建应用一致性快照，则会创建文件一致性快照。
    - 对于 Linux Vm，备份采用文件一致的快照。 对于应用一致性快照，您需要手动自定义前期/后期脚本。
    - 可以通过并行备份每个 VM 磁盘来优化备份。 对于每个要备份的磁盘，Azure 备份将读取磁盘上的块，并只存储已更改的数据。 
1. 创建快照后，数据将传输到保管库。 
    - 只会更改，因为上一次备份将复制的数据块。
    - 不会加密数据。 Azure 备份可以备份使用 Azure 磁盘加密加密的 Azure Vm。
    - 快照数据可能不会立即复制到保管库。 在高峰时间，备份可能需要几个小时。 为 vm 备份总时间将是 24 小时内每日备份策略。
1. 将数据发送到保管库后，会删除快照，并创建恢复点。

Azure Vm 需要 internet 访问权限的控制命令。 如果你要备份 VM （例如，SQL Server 数据库备份） 内的工作负荷后, 端数据也需要访问 internet。 

![备份 Azure VM](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>体系结构：直接在本地 Windows Server 计算机或 Azure VM 的文件或文件夹的备份

1. 若要设置方案，请下载，并在计算机上安装 MARS 代理。 然后，，选择要备份、 何时运行备份，以及它们将保留时间在 Azure 中。
1. 根据备份设置运行初始备份。
1. MARS 代理使用 VSS 来选择用于备份的卷的时间点快照。
    - MARS 代理使用仅在 Windows 系统写入操作捕获快照。
    - 代理不使用任何应用程序 VSS 编写器，因为它不会捕获应用程序一致的快照。
1. 生成使用 VSS 快照后, MARS 代理配置备份时指定的缓存文件夹中创建虚拟硬盘 (VHD)。 代理还将存储为每个数据块的校验和。
1. 根据你指定的计划的增量备份运行，除非你运行即席备份。
1. 在增量备份中，将会标识已更改的文件，并创建新的 VHD。 压缩和加密的 VHD，然后发送到保管库。
1. 增量备份完成后，新的 VHD 合并在一起在初始复制之后创建的 VHD。 此合并的 VHD 提供要用于比较的正在进行备份的最新状态。

![使用 MARS 代理的本地 Windows Server 计算机的备份](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>体系结构：备份到 DPM/MABS

1. 你想要保护的计算机上安装 DPM 或 MABS 保护代理。 然后，您将计算机添加到 DPM 保护组。
    - 若要保护本地计算机，DPM 或 MABS 服务器必须位于本地。
    - 若要保护 Azure VM，MABS 服务器必须位于 Azure 中，并作为 Azure VM 运行。
    - 使用 DPM/MABS 可以保护备份卷、 共享、 文件和文件夹。 此外可以保护计算机的系统状态 （裸机），并可以使用应用程序感知备份设置保护特定的应用。
1. 如果设置了保护的计算机或 DPM/MABS 中的应用时，您选择备份到 MABS/DPM 本地磁盘进行短期存储和 Azure 的在线保护。 您还可以指定应何时运行备份到本地 DPM/MABS 存储和到 Azure 的联机备份应在何时运行。
1. 受保护的工作负荷的磁盘备份到 MABS/DPM 本地磁盘中，根据你指定的计划。
4. DPM/MABS 磁盘备份到保管库的 DPM/MABS 服务器运行的 MARS 代理。

![计算机和 DPM 或 MABS 保护工作负荷的备份](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM 存储

Azure VM 使用磁盘来存储其操作系统、应用和数据。 每个 Azure VM 具有至少两个磁盘： 一个磁盘用于操作系统和临时磁盘。 Azure Vm 还可能包含应用程序数据的数据磁盘。 磁盘以 VHD 的形式进行存储。

- Vhd 存储在 Azure 中的标准或高级存储帐户中作为页 blob:
    - **标准存储：** 为运行不关注延迟的工作负荷的 VM 提供可靠、低成本的磁盘支持。 标准固态硬盘 (SSD) 磁盘或标准硬盘驱动器 (HDD) 的磁盘，可以使用标准存储。
    - **高级存储：** 高性能磁盘支持。 使用高级·SSD 磁盘。
- 磁盘具有不同的性能层：
    - **标准 HDD 磁盘：** 基于 HDD，用作经济高效的存储。
    - **标准 SSD 磁盘：** 将元素的高级 SSD 磁盘和标准 HDD 磁盘合并。 提供更一致的性能和可靠性 HDD，但仍具有成本效益。
    - **高级 SSD 磁盘：** 支持的 Ssd，并为正在运行 O 大量工作负荷的 Vm 提供高性能和低延迟。
- 磁盘可以是托管磁盘或非托管磁盘：
    - **非托管的磁盘：** 传统类型的 Vm 使用的磁盘。 对于这些磁盘，可以创建自己的存储帐户，并在创建磁盘时指定该存储帐户。 然后需要找出最大限度地为您的 Vm 的存储资源。
    - **托管磁盘：** Azure 创建，并为你管理的存储帐户。 指定磁盘大小和性能层，然后 Azure 将为你创建托管的磁盘。 在添加磁盘和缩放 Vm 时，Azure 将处理的存储帐户。

Vm 磁盘存储空间和可用的磁盘类型的详细信息，请参阅以下文章：

- [适用于 Windows Vm 的 azure 托管的磁盘](../virtual-machines/windows/managed-disks-overview.md)
- [适用于 Linux Vm 的 azure 托管的磁盘](../virtual-machines/linux/managed-disks-overview.md)
- [适用于 Vm 的可用磁盘类型](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>备份和还原 Azure Vm 使用高级存储 

可以使用高级存储的 Azure 备份来备份 Azure Vm:

- 在备份虚拟机使用高级存储的过程中，备份服务会创建名为的临时暂存位置*AzureBackup-*，存储帐户中。 暂存位置的大小等于恢复点快照大小。
- 确保高级存储帐户有足够的可用空间，可以容纳临时暂存位置。 [了解详细信息](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits)。 不要修改暂存位置。
- 备份作业完成后，将删除暂存位置。
- 用于暂存位置的存储的价格与[高级存储定价](../virtual-machines/windows/disks-types.md#billing)相一致。

还原时 Azure Vm 使用高级存储，你可以还原到高级或标准存储。 通常情况下，会将它们还原到高级存储。 但如果您需要从虚拟机文件的一个子集，则可能是经济地将它们还原到标准存储。

### <a name="back-up-and-restore-managed-disks"></a>备份和还原托管的磁盘

可以使用托管磁盘备份 Azure Vm:

- 像备份其他任何 Azure VM 一样备份使用托管磁盘的 VM。 可以直接从虚拟机设置备份 VM，或者，可以在恢复服务保管库中为 VM 启用备份。
- 通过基于托管磁盘的 RestorePoint 收集，可以在托管磁盘上备份 VM。
- Azure 备份还支持使用托管磁盘时使用 Azure 磁盘加密加密的备份 Vm。

使用托管磁盘还原 Vm 时，可以还原到使用托管磁盘的完整 VM 或存储帐户：

- 在还原过程中，Azure 将处理的托管的磁盘。 如果使用的存储帐户选项，则管理在还原过程中创建的存储帐户。
- 如果要还原已加密的托管的 VM，请确保 VM 的密钥，并开始还原过程之前，密钥保管库中存在机密。

## <a name="next-steps"></a>后续步骤

- 复查支持矩阵，向[了解有关支持的功能和限制的备份方案](backup-support-matrix.md)。
- 设置备份其中一种情形：
    - [Azure Vm 备份](backup-azure-arm-vms-prepare.md)。
    - 不使用备份服务器[直接备份 Windows 计算机](tutorial-backup-windows-server-to-azure.md)。
    - [设置 MABS](backup-azure-microsoft-azure-backup.md) 以备份到 Azure，然后将工作负荷备份到 MABS。
    - [设置 DPM](backup-azure-dpm-introduction.md) 以备份到 Azure，然后将工作负荷备份到 DPM。


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png


---
title: Azure 备份体系结构
description: 概述 Azure 备份服务使用的体系结构、组件和流程。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: raynew
ms.openlocfilehash: 84890c0658970aa9f61a06764cf902a5e5ee4379
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812550"
---
# <a name="azure-backup-architecture"></a>Azure 备份体系结构

可以使用 [Azure 备份服务](backup-overview.md)将数据备份到 Microsoft Azure 云。 本文汇总了 Azure 备份体系结构、组件和流程。 


## <a name="what-does-azure-backup-do"></a>Azure 备份有什么功能？

Azure 备份可以备份数据、计算机状态，以及本地计算机和 Azure VM 上运行的工作负荷。 有许多 Azure 备份方案。

- **备份本地计算机**：
    - 可以使用 Azure 备份将本地计算机直接备份到 Azure。
    - 可以使用 System Center Data Protection Manager (DPM) 或 Microsoft Azure 备份服务器 (MABS) 保护本地计算机，然后使用 Azure 备份将 DPM/MABS 上的受保护数据备份到 Azure。
- **备份 Azure VM**：
    - 可以直接使用 Azure 备份来备份 Azure VM。
    - 可以使用 Azure 中运行的 DPM 或 MABS 保护 Azure VM，然后使用 Azure 备份来备份 DPM/MABS 上的受保护数据。

详细了解[可以备份哪些内容](backup-overview.md)，以及[支持的备份方案](backup-support-matrix.md)。


## <a name="where-is-data-backed-up"></a>数据备份到何处？

Azure 备份将备份的数据存储在恢复服务保管库中。 保管库是 Azure 中的联机存储实体，用于保存备份副本、恢复点、备份策略等数据。

- 使用恢复服务保管库可以方便地组织备份数据，并将管理开销降至最低。
- 在每个 Azure 订阅中，最多可以创建 500 个恢复服务保管库。 
- 可以监视保管库中的备份项，包括 Azure VM 和本地计算机。
- 可以使用 Azure [基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 来管理对保管库的访问。
- 指定如何复制保管库中的数据以实现冗余：
    - **LRS**：可以使用本地冗余存储 (LRS) 来防范数据中心的故障。 LRS 将数据复制到存储缩放单元。 [了解详细信息](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)。
    - **GRS**：可以使用异地冗余存储 (GRS)：防范区域范围的服务中断。 GRS 将数据复制到次要区域。 [了解详细信息](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。 
    - 用于备份的恢复服务保管库默认使用 GRS。 



## <a name="how-does-azure-backup-work"></a>Azure 备份的工作原理是什么？

Azure 备份基于默认备份策略或自定义备份策略运行备份作业。 Azure 备份创建备份的方式取决于方案。

**方案** | **详细信息** 
--- | ---
**直接备份本地计算机** | 若要直接备份本地计算机，Azure 备份将使用 Microsoft Azure 恢复服务 (MARS) 代理。 该代理安装在要备份的每台计算机上。 <br/><br/> 此类备份不适用于本地 Linux 计算机。 
**直接备份 Azure VM** | 若要直接备份 Azure VM，需在首次针对 VM 运行备份时，在该 VM 上安装一个 Azure VM 扩展。 
**备份 DPM 或 MABS 保护的计算机和应用** | 在此方案中，计算机/应用首先备份到 DPM 或 MABS 本地存储。 然后，Azure 备份将 DPM/MABS 中的数据备份到保管库。 可以通过本地运行的 DPM/MABS 保护本地计算机上。 可以通过 Azure 中运行的 DPM/MABS 保护 Azure VM。

[获取概述](backup-overview.md)，并了解每种方案[支持的备份方式](backup-support-matrix.md)。


### <a name="backup-agents"></a>备份代理

Azure 备份根据备份类型提供不同的代理。

**代理** | **详细信息** 
--- | --- 
**Microsoft Azure 恢复服务 (MARS) 代理** | 此代理在独立的本地 Windows 服务器上运行，可备份文件、文件夹和系统状态<br/><br/> 此代理在 DPM/MABS 服务器上运行，可备份 DPM/MABS 本地存储磁盘。 计算机和应用在本地备份到此 DPM/MABS 磁盘。
**Azure VM 扩展** | 若要备份 Azure VM，需将一个备份扩展添加到 VM 上运行的 Azure VM 代理。 


## <a name="backup-types"></a>备份类型

**备份类型** | **详细信息** | **使用情况**
--- | --- | ---
**完整** | 备份包含整个数据源。<br/><br/> 完整备份会占用更多的网络带宽。 | 用于初始备份。
**差异** |  存储自初始完整备份以来发生更改的块。 使用较少的网络带宽和存储量，且不保留未更改数据的冗余副本。<br/><br/> 效率不高，因为需要传输并存储每次后续备份之后未发生更改的数据块。 | Azure 备份不使用此备份类型。
**增量** | 存储和网络效率高。 仅存储自上次备份以来发生更改的数据块。 <br/><br/> 无需使用增量备份，无需使用完整备份进行补充。 | DPM/MABS 使用此备份类型来备份磁盘，备份到 Azure 的所有方案都使用此备份类型。

### <a name="comparison"></a>比较

存储消耗、恢复时间目标 (RTO) 和网络消耗因每种备份类型而异。 下图比较了不同的备份类型。
- 数据源 A 由每月备份的 10 个存储块 A1-A10 组成。
- 第一个月，存储块 A2、A3、A4 和 A9 变化，第二个月，存储块 A5 变化。
- 对于差异备份，在第二个月，将备份已更改的块 A2、A3、A4 和 A9。 第三个月，会再次备份这些相同的存储块，以及已更改的存储块 A5。 下次进行完整备份之前，将继续对已更改的存储块进行备份。
- 对于增量备份，在第一个月进行完整备份后，存储块 A2、A3、A4 和 A9 将标记为已更改，然后转移到第二个月。 在第三个月，仅标记已更改的存储块 A5，并进行传输。 

![备份方法比较图](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>备份功能

下表汇总了不同备份类型的功能。

**功能** | **本地 Windows 计算机（直接备份）** | **Azure VM** | **DPM/MABS 中的计算机/应用**
--- | --- | --- | ---
备份到保管库 | ![是][green] | ![是][green] | ![是][green] 
依次备份到 DPM/MABS 磁盘和 Azure | | | ![是][green] 
压缩发送的备份数据 | ![是][green] | 传输数据时不使用压缩。 存储消耗略有提高，但还原速度更快。  | ![是][green] 
运行增量备份 |![是][green] |![是][green] |![是][green] 
备份已删除重复数据的磁盘 | | | ![部分][yellow]<br/><br/> 仅适用于本地部署的 DPM/MABS 服务器。 

![表键](./media/backup-architecture/table-key.png)


## <a name="architecture-direct-backup-of-on-premises-windows-machines"></a>体系结构：直接备份本地 Windows 计算机

1. 若要设置方案，请在计算机中下载并安装 Microsoft Azure 恢复服务 (MARS) 代理，选择要备份的内容、运行备份的时间，以及备份在 Azure 中保留的期限。
2. 初始备份根据备份设置运行。
3. MARS 代理使用 Windows 卷影复制 (VSS) 服务来创建选择进行备份的卷的时间点快照。
    - MARS 代理仅使用 Windows 系统写入来捕获快照。
    - 该代理不使用任何应用程序 VSS 写入器，因此不会捕获应用一致性快照。
3. 使用 VSS 创建快照后，MARS 代理将在配置备份时指定的缓存文件夹中创建一个 VHD，并存储每个数据块的校验和。 
4. 除非运行即席备份，否则增量备份会根据指定的计划运行。
5. 在增量备份中，将会标识已更改的文件，并创建新的 VHD。 该 VHD 经过压缩和加密，并会发送到保管库。
6. 增量备份完成后，新 VHD 将与初始复制后创建的 VHD 合并，提供最新的状态用于对现行备份进行比较。 

![使用 MARS 代理备份本地 Windows 服务器](./media/backup-architecture/architecture-on-premises-mars.png)


## <a name="architecture-direct-backup-of-azure-vms"></a>体系结构：直接备份 Azure VM

1. 为 Azure VM 启用备份时，将会根据指定的计划运行备份。
2. 首次备份期间，如果 VM 已运行，则会在 VM 上安装备份扩展。
    - 对于 Windows VM，将安装 VMSnapshot 扩展。
    - 对于 Linux VM，将安装 VMSnapshot Linux 扩展。
3. 该扩展创建存储级快照。 
    - 对于正在运行的 Windows VM，Azure 备份将与 VSS 相协调，以创建 VM 的应用一致性快照。 Azure 备份默认创建完整的 VSS 备份。 如果 Azure 备份无法创建应用一致性快照，则会创建文件一致性快照。
    - 对于 Linux VM，Azure 备份将创建文件一致性备份。 对于应用一致性快照，需要手动自定义前脚本/后脚本。
    - 可以通过并行备份每个 VM 磁盘来优化备份。 对于每个要备份的磁盘，Azure 备份将读取磁盘上的块，并只存储已更改的数据。 
4. 创建快照后，数据将传输到保管库。 
    - 只会复制自上次备份以来发生更改的数据块。
    - 不会加密数据。 Azure 备份可以备份通过 Azure 磁盘加密 (ADE) 进行加密的 Azure VM。
    - 快照数据可能不会立即复制到保管库。 在高峰期，可能需要好几个小时才能完成复制。 每日备份策略规定的 VM 备份总时间不会超过 24 小时。
5. 将数据发送到保管库后，将删除快照，并创建恢复点。


![备份 Azure VM](./media/backup-architecture/architecture-azure-vm.png)


## <a name="architecture-back-up-to-dpmmabs"></a>体系结构：备份到 DPM/MABS

1. 在要保护的计算机上安装 DPM 或 MABS 保护代理，并将计算机添加到 DPM 保护组。
    - 若要保护本地计算机，DPM 或 MABS 服务器必须位于本地。
    - 若要保护 Azure VM，DPM 或 MABS 服务器必须位于 Azure 中，并作为 Azure VM 运行。
    - 使用 DPM/MABS 可以保护备份的卷、共享、文件和文件夹。 可以保护计算机系统状态/裸机，并可以使用应用感知的备份设置来保护特定的应用。
2. 为 DPM 中的计算机或应用设置保护时，请选择备份到 DPM 本地磁盘进行短期存储，并备份到 Azure（联机保护）。 此外，可以指定何时要运行备份到本地 DPM/MABS 存储的操作，以及何时要运行联机备份到 Azure 的操作。
3. 受保护工作负荷的磁盘将根据指定的计划备份到本地 DPM 磁盘和 Azure。
4. 联机备份到保管库的操作由 DPM/MABS 服务器上运行的 MARS 代理进行处理。

![备份 DPM 或 MABS 保护的计算机/工作负荷](./media/backup-architecture/architecture-dpm-mabs.png)







## <a name="azure-vm-storage"></a>Azure VM 存储

- Azure VM 使用磁盘来存储其操作系统、应用和数据。
- Azure VM 至少包含两个磁盘。 其中一个磁盘用于操作系统，另一个用作临时磁盘。 Azure VM 还可以使用数据磁盘来存储应用数据。 磁盘以 VHD 的形式进行存储。
- 在 Azure 上的标准或高级存储帐户中，VHD 以页 Blob 的形式进行存储。
    - 标准存储：为运行不关注延迟的工作负荷的 VM 提供可靠、低成本的磁盘支持。 标准存储可以使用标准 SSD 磁盘或标准 HDD 磁盘。
    - 高级存储：高性能磁盘支持。 使用高级·SSD 磁盘。
- 磁盘具有不同的性能层：
    - 标准 HDD 磁盘：基于 HDD，用作经济高效的存储。
    - 标准 SSD 磁盘：结合了高级 SSD 磁盘和标准 HDD 磁盘的特点，提供的一致性能和可靠性超过 HDD，但仍具备一定的成本效益。
    - 高级·SSD 磁盘：基于 SSD，为运行 I/O 密集型工作负荷的 VM 提供高性能和低延迟。
- 磁盘可以是托管磁盘或非托管磁盘：
    - 非托管磁盘：VM 使用的传统磁盘类型。 对于这些磁盘，可以创建自己的存储帐户，并在创建磁盘时指定该存储帐户。 必须确定如何最大限度地利用 VM 的存储资源。
    - 托管磁盘：Azure 将为你处理存储帐户的创建和管理。 你只需指定磁盘大小和性能层，Azure 将为你创建和管理磁盘。 当你添加磁盘和缩放 VM 时，Azure 将处理存储。

了解详细信息：

- 详细了解 [Windows](../virtual-machines/windows/about-disks-and-vhds.md) 和 [Linux](../virtual-machines/linux/about-disks-and-vhds.md) VM 的磁盘存储。
- 了解[标准](../virtual-machines/windows/standard-storage.md)和[高级](../virtual-machines/windows/premium-storage.md)存储。


### <a name="backing-up-and-restoring-azure-vms-with-premium-storage"></a>备份和还原使用高级存储的 Azure VM 

可以通过 Azure 备份来备份使用高级存储的 Azure VM：

- 备份使用高级存储的 VM 时，备份服务将在存储帐户中创建名为“AzureBackup-”的临时暂存位置。 暂存位置大小与恢复点快照大小相同。
- 确保高级存储帐户有足够的可用空间，可以容纳临时暂存位置。 [了解详细信息](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)。 不要修改暂存位置。
- 备份作业完成后，将删除暂存位置。
- 用于暂存位置的存储的价格与[高级存储定价](../virtual-machines/windows/premium-storage.md#pricing-and-billing)相一致。

还原使用高级存储的 Azure VM 时，可将其还原到高级或标准存储。 通常你会还原到高级存储，但如果你只需要 VM 中的一部分文件，则还原到标准存储可能更具成本效益。

### <a name="backing-up-and-restoring-managed-disks"></a>备份和还原托管磁盘

可以备份使用托管磁盘的 Azure VM。
- 像备份其他任何 Azure VM 一样备份使用托管磁盘的 VM。 可以直接从虚拟机设置备份 VM，或者，可以在恢复服务保管库中为 VM 启用备份。
- 通过基于托管磁盘的 RestorePoint 收集，可以在托管磁盘上备份 VM。
- Azure 备份也支持备份使用 Azure 磁盘加密 (ADE) 进行加密的托管磁盘 VM。

还原使用托管磁盘的 VM 时，可以还原到使用托管磁盘的整个 VM，或者还原到存储帐户。
- 在还原过程中，Azure 会处理托管磁盘；可以使用存储帐户选项来管理在还原期间创建的存储帐户。
- 如果还原已加密的托管 VM，在开始还原过程之前，VM 密钥和机密应在 Key Vault 中存在。




## <a name="next-steps"></a>后续步骤

- [查看](backup-support-matrix.md)支持矩阵，以了解备份方案支持的功能和限制。
- 为以下方案之一设置备份：
    - [备份 Azure VM](backup-azure-arm-vms-prepare.md)
    - 不使用备份服务器[直接备份 Windows 计算机](tutorial-backup-windows-server-to-azure.md)。
    - [设置 MABS](backup-azure-microsoft-azure-backup.md) 以备份到 Azure，然后将工作负荷备份到 MABS。
    - [设置 DPM](backup-azure-dpm-introduction.md) 以备份到 Azure，然后将工作负荷备份到 DPM。


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png


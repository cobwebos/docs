---
title: 什么是 Azure 备份？
description: 概述 Azure 备份服务及其如何有助于实现业务连续性和灾难恢复 (BCDR) 策略。
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: 49ef90c874e920ef92dd5206447081a6aa4f7988
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172202"
---
# <a name="what-is-the-azure-backup-service"></a>什么是 Azure 备份服务？

Azure 备份服务将数据备份到 Microsoft Azure 云。 可以备份本地计算机和工作负荷，以及 Azure 虚拟机 (VM)。

## <a name="why-use-azure-backup"></a>为何使用 Azure 备份？

Azure 备份具有以下主要优势：

- **卸载本地备份**：Azure 备份提供一个简单的解决方案，可以将本地资源备份到云。 获取短期和长期备份，不需部署复杂的本地备份解决方案。
- **备份 Azure IaaS VM**：Azure 备份提供独立且隔离的备份，可以防范原始数据的意外破坏。 备份存储在可以对恢复点进行内置托管的恢复服务保管库中。 配置和可伸缩性很简单，备份经过优化，可以轻松地根据需要还原。
- **轻松缩放** - Azure 备份利用 Azure 云的基础功能和无限缩放功能实现高可用性 - 无需维护，也无需监视开销。
- **无限数据传输**：Azure 备份不会限制传输的入站或出站数据量，不会对传输的数据收费。
  - 出站数据是指还原操作期间从恢复服务保管库传输的数据。
  - 如果使用 Azure 导入/导出服务执行脱机初始备份以导入大量数据，则入站数据将产生相关费用。  [了解详细信息](backup-azure-backup-import-export.md)。
- **保护数据安全**：Azure 备份为传输中的数据和静态数据提供保护解决方案。
- **获取应用一致性备份**：应用程序一致性备份意味着恢复点包含还原备份副本所需的所有数据。 Azure 备份提供了应用程序一致性备份，确保了还原数据时无需额外的修补程序。 还原应用程序一致型数据可减少还原时间，因此可快速恢复到运行状态。
- **保留短期和长期数据**：可将恢复服务保管库用于短期和长期数据保留。 Azure 不会限制恢复服务保管库中数据的保留时间长度。 可将数据保留任意时间。 Azure 备份的限制为每个受保护实例仅限 9999 个恢复点。
- **自动存储管理** - 混合环境常常需要异类存储（部分在本地，部分在云端）。 通过 Azure 备份，使用本地存储设备时无需付费。 Azure 备份会自动分配和管理备份存储，且采用即用即付模型，因此，你只需为消耗的存储付费。 [详细了解](https://azure.microsoft.com/pricing/details/backup)定价情况。
- **多个存储选项** - Azure 备份提供两种类型的复制来保持存储/数据的高可用性。
  - [本地冗余存储 (LRS)](../storage/common/storage-redundancy-lrs.md) 将数据中心的存储缩放单元中的数据复制三次（创建三个数据副本）。 数据的所有副本存在于同一区域。 LRS 是一种低成本选项，用于保护数据免受本地硬件故障的影响。
  - [异地冗余存储 (GRS)](../storage/common/storage-redundancy-grs.md) 是默认的和推荐的复制选项。 GRS 将数据复制到离源数据主位置数英里之外的次要区域中。 GRS 的成本比 LRS 的高，但 GRS 可让数据更为持久，即使出现区域性故障也是如此。

## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Azure 备份与 Azure Site Recovery 的区别是什么？

Azure 备份和 Azure Site Recovery 服务有助于企业实现业务连续性和灾难恢复 (BCDR) 策略。 BCDR 包含两个主要目的：

- 在出现故障时确保业务数据的安全和可恢复性。
- 在计划内和计划外停机期间确保应用和工作负荷启动并运行。

两项服务都提供不同但互补的功能。

- **Azure Site Recovery**：Site Recovery 为本地计算机和 Azure VM 提供灾难恢复解决方案。 可以将计算机从主位置复制到辅助位置。 出现灾难时，可以将计算机故障转移到辅助位置，从辅助位置访问它们。 一切恢复正常后，可以对计算机执行故障回复，在主站点恢复它们。
- **Azure 备份**：Azure 备份服务可以从本地计算机和 Azure VM 备份数据。 可以在粒度级别备份和恢复数据，包括对文件、文件夹和计算机系统状态进行备份，以及进行应用感知型数据备份。 Azure 备份处理数据时所在的粒度级别比 Site Recovery 更细。 例如，如果便携式计算机上的演示文稿损坏，则可使用 Azure 备份来还原该演示文稿。 若要确保 VM 配置和数据的安全性和可访问性，则可使用 Site Recovery。  

以表格的形式列出 BCDR 需求。

**目标** | **详细信息** | **比较**
--- | --- | ---
**数据备份/保留** | 从合规的角度来看，如有必要，可以将备份数据保留和存储数天、数月甚至数年。 | 可以通过 Azure 备份之类的备份解决方案细致地选取要备份的数据，并对备份和保留策略进行优化。<br/><br/> Site Recovery 不允许这样细致的优化。
**恢复点目标 (RPO)** | 在需要执行恢复的情况下可接受的数据丢失量。 | 备份的 RPO 的可变性更高。<br/><br/> VM 备份的 RPO 通常为一天，而数据库备份的 RPO 可以低至 15 分钟。<br/><br/> Site Recovery 提供的 RPO 低是因为复制持续且频繁，因此源和副本之间的差异小。
**恢复时间目标 (RTO)** |完成恢复或还原所需的时间量。 | 由于 RPO 较大，备份解决方案需要处理的数据量通常更多，这会导致 RTO 较长。 例如，根据从异地转送磁带所需的时间，从磁带还原数据可能需要数天的时间。

## <a name="what-backup-scenarios-are-supported"></a>支持哪些备份方案？

Azure 备份可以备份本地计算机和 Azure VM。

**计算机** | **备份方案**
--- | ---
**本地备份** |  1) 在本地 Windows 计算机上运行 Azure 备份 Microsoft Azure 恢复服务 (MARS) 代理，以备份单个文件和系统状态。 <br/><br/>2) 将本地计算机备份到备份服务器（System Center Data Protection Manager (DPM) 或 Microsoft Azure 备份服务器 (MABS)），然后将备份服务器配置为备份到 Azure 中的 Azure 备份恢复服务保管库。
**Azure VM** | 1) 为单个 Azure VM 启用备份。 启用备份时，Azure 备份会在 VM 上运行的 Azure VM 代理中安装一个扩展。 该代理备份整个 VM。<br/><br/> 2) 在 Azure VM 上运行 MARS 代理。 若要备份 VM 上的单个文件和文件夹，此功能将十分有用。<br/><br/>

## <a name="why-use-a-backup-server"></a>为何要使用备份服务器？

将计算机和应用备份到 MABS/DPM 存储，然后将 DPM/MABS 存储备份到保管库的优点如下所述：

- 备份到 MABS/DPM 可以在文件/文件夹/卷备份以及计算机状态备份（裸机、系统状态）的基础上提供应用感知型备份，后者已针对常用应用（例如 SQL Server、Exchange 和 SharePoint）进行优化。
- 对于本地计算机，不需在要备份的每台计算机上安装 MARS 代理。 每台计算机运行 DPM/MABS 保护代理，而 MARS 代理只在 MABS/DPM 上运行。
- 有更多适合运行备份的灵活性和粒度计划选项。
- 可以管理多台计算机的备份，这些计算机已在单个控制台中集中到保护组。 当应用的分层跨越多台计算机，而你需要将它们一起进行备份时，此方法很有用。

详细了解使用备份服务器时的[备份工作原理](backup-architecture.md#architecture-back-up-to-dpmmabs)，以及备份服务器的[支持要求](backup-support-matrix-mabs-dpm.md)。

## <a name="what-can-i-back-up"></a>可以备份哪些内容？

**计算机** | **备份方法** | **备份**
--- | --- | ---
**本地 Windows VM** | 运行 MARS 代理 | 备份文件、文件夹、系统状态。<br/><br/> 不支持 Linux 计算机。
**本地计算机** | 备份到 DPM/MABS | 备份受 [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) 或 [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) 保护的任何内容，包括文件/文件夹/共享/卷以及特定于应用的数据。
**Azure VM** | 运行 Azure VM 代理备份扩展 | 备份整个 VM
**Azure VM** | 运行 MARS 代理 | 备份文件、文件夹、系统状态。<br/><br/> 不支持 Linux 计算机。
**Azure VM** | 备份到 Azure 中运行的 MABS/DPM | 备份受 [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) 或 [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) 保护的任何内容，包括文件/文件夹/共享/卷以及特定于应用的数据。

## <a name="what-backup-agents-do-i-need"></a>我需要什么样的备份代理？

**方案** | **代理**
--- | ---
**备份 Azure VM** | 不需要代理。 运行首次 Azure VM 备份时，将在 Azure VM 上安装用于备份的 Azure VM 扩展。<br/><br/> 提供 Windows 和 Linux 支持。
**备份本地 Windows 计算机** | 在计算机上直接下载、安装和运行 MARS 代理。
**使用 MARS 代理备份 Azure VM** | 在计算机上直接下载、安装和运行 MARS 代理。 MARS 代理可与备份扩展一同运行。
**将本地计算机和 Azure VM 备份到 DPM/MABS** | DPM 或 MABS 保护代理在要保护的计算机上运行。 MARS 代理在要备份到 Azure 的 DPM 服务器/MABS 上运行。

## <a name="which-backup-agent-should-i-use"></a>我应该使用哪个备份代理？

**备份** | **解决方案** | **限制**
--- | --- | ---
**我要备份整个 Azure VM** | 为 VM 启用备份。 备份扩展会在 Windows 或 Linux Azure VM 上自动配置。 | 备份整个 VM <br/><br/> 就 Windows VM 来说，备份为应用一致性备份。 就 Linux 来说，备份为文件一致性备份。 如果需要对 Linux VM 进行应用感知，则必须使用自定义脚本进行相应配置。
**我想要备份 Azure VM 上的特定文件/文件夹** | 在 VM 上部署 MARS 代理。
**我想要直接备份本地 Windows 计算机** | 在计算机上安装 MARS 代理。 | 可以将文件、文件夹和系统状态备份到 Azure。 备份不是应用感知型。
**我想要直接备份本地 Linux 计算机** | 需先部署 DPM 或 MABS，然后才能将内容备份到 Azure。 | 不支持备份 Linux 主机，只能备份 Hyper-V 或 VMWare 上托管的 Linux 来宾计算机。
**我想要备份本地运行的应用** | 若要进行应用感知的备份，计算机必须受 DPM 或 MABS 的保护。
**我想要对 Azure VM 使用精细且灵活的备份和恢复设置** | 使用在 Azure 中运行的 MABS/DPM 来保护 Azure VM，这样可以在备份计划方面获得更大的灵活性，并在保护和还原文件、文件夹、卷、应用和系统状态方面获得最大的灵活性。

## <a name="backup-and-retention"></a>备份和保留

Azure 备份针对每个受保护实例实施 9999 个恢复点（也称为备份副本或快照）的限制。 

- 受保护的实例是计算机、服务器（物理或虚拟）或配置为向 Azure 备份数据的工作负荷。 保存数据的备份副本时，将保护实例。
- 数据的备份副本是保护项。 如果源数据丢失或损坏，备份副本可还原源数据。

下表显示了每个组件的最大备份频率。 备份策略配置确定了恢复点的消耗速度。 例如，如果每天创建一个恢复点，可以保留恢复点 27 年，27 年后配额会耗尽。如果每月创建一个恢复点，可以保留恢复点 833 年。备份服务未针对恢复点实施过期时间限制。

|  | Azure 备份 (MARS) 代理| System Center DPM | Azure 备份服务器 | Azure IaaS VM 备份 |
| --- | --- | --- | --- | --- |
| 备份频率<br/> （到恢复服务保管库） |每天三次备份 |每天两次备份 |每天两次备份 |每天一次备份 |
| 备份频率<br/> （到磁盘） |不适用 |SQL Server 每隔 15 分钟<br/><br/> 其他工作负荷每隔 1 小时 |SQL Server 每隔 15 分钟<br/><br/> 其他工作负荷每隔 1 小时 |不适用 |
| 保留期选项 |每日、每周、每月、每年 |每日、每周、每月、每年 |每日、每周、每月、每年 |每日、每周、每月、每年 |
| 每个受保护实例的恢复点数上限 |9999|9999|9999|9999|
| 最长数据保留期 |取决于备份频率 |取决于备份频率 |取决于备份频率 |取决于备份频率 |
| 本地磁盘上的恢复点 |不适用 | 对于文件服务器为 64<br/><br/> 对于应用程序服务器为 448 | 对于文件服务器为 64<br/><br/> 对于应用程序服务器为 448 |不适用 |
| 磁带上的恢复点 |不适用 |不受限制 |不适用 |不适用 |

## <a name="how-does-azure-backup-work-with-encryption"></a>Azure 备份如何使用加密？

**加密** | **本地备份** | **备份 Azure VM** | **在 Azure VM 上备份 SQL Server**
--- | --- | --- | ---
静态加密<br/> （对保留/存储的数据进行加密） | 客户指定的密码短语用于加密数据 | Azure [存储服务加密 (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 用于加密存储在保管库中的数据。<br/><br/> 备份会在存储数据之前自动加密数据。 Azure 存储会在检索数据之前解密数据。 目前不支持使用客户托管的 SSE 密钥。<br/><br/> 可以将使用 [Azure 磁盘加密 (ADE)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) 来加密 OS 和数据磁盘的 VM 进行备份。 Azure 备份支持仅使用 BEK 加密的 VM 以及同时使用 BEK 和 [ KEK ](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/) 加密的 VM。 查看[限制](backup-azure-vms-encryption.md#encryption-support)。 | Azure 备份支持备份启用了 [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) 的 SQL Server 数据库或服务器。 Azure 备份支持由 Azure 托管密钥的或由客户托管密钥 (BYOK) 的 TDE。<br/><br/> Azure 备份不会在备份过程中执行任何 SQL 加密。
传输中加密<br/> （从一个位置向另一个位置移动的数据的加密） | 数据使用 AES256 加密，并通过 HTTPS 发送到 Azure 中的保管库 | 在 Azure 中，Azure 存储与保管库之间传输的数据受 HTTPS 保护。 此数据保留在 Azure 主干网络上。<br/><br/> 对于文件恢复，iSCSI 会保护保管库和 Azure VM 之间传输的数据。 安全隧道可保护 iSCSI 通道。 | 在 Azure 中，Azure 存储与保管库之间传输的数据受 HTTPS 保护。<br/><br/> 文件恢复与 SQL 无关。

## <a name="next-steps"></a>后续步骤

- [查看](backup-architecture.md)不同备份方案的体系结构和组件。
- [验证](backup-support-matrix.md)对备份和 [Azure VM 备份](backup-support-matrix-iaas.md)的支持要求和限制。

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

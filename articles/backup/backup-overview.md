---
title: 什么是 Azure 备份？
description: 概述 Azure 备份服务，以及如何将它部署为业务连续性和灾难恢复 (BCDR) 策略的一部分。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7156042243b1ba28cea712dc3722600b9fc46c42
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360613"
---
# <a name="what-is-azure-backup"></a>什么是 Azure 备份？

Azure 备份服务将数据备份到 Microsoft Azure 云。 可以备份本地计算机和工作负荷，以及 Azure 虚拟机 (VM)。


## <a name="why-use-azure-backup"></a>为何使用 Azure 备份？

Azure 备份具有以下主要优势：

- **卸载本地备份**：Azure 备份提供一个简单的解决方案，可以将本地资源备份到云。 获取短期和长期备份，不需部署复杂的本地备份解决方案。 不需磁带或场外备份。
- **备份 Azure IaaS VM**：Azure 备份提供独立且隔离的备份，可以防范原始数据的意外破坏。 备份存储在可以对恢复点进行内置托管的恢复服务保管库中。 配置和可伸缩性很简单，备份经过优化，可以轻松地根据需要还原。
- **轻松缩放** - Azure 备份利用 Azure 云的基础功能和无限缩放功能实现高可用性 - 无需维护，也无需监视开销。 可设置警报来获取相关事件信息，但无需担忧云端数据的高可用性。
- **获取无限的数据传输** - Azure 备份不会限制传输的入站或出站数据量。 Azure 备份也不会对传输的数据收费。 但如果使用 Azure 导入/导出服务来导入大量数据，则入站数据将产生相关费用。 有关此费用的详细信息，请参阅 [Azure 备份中的脱机备份工作流](backup-azure-backup-import-export.md)。 出站数据是指还原操作期间从恢复服务保管库传输的数据。
- **保护数据安全**：可以通过数据加密在公有云中安全地传输和存储数据。 加密密码将存储在本地，绝不会传输或存储到 Azure 中。 如有必要还原任何数据，只需具有加密密码或密钥即可。
- **获取应用一致性备份**：应用程序一致性备份意味着恢复点包含还原备份副本所需的所有数据。 Azure 备份提供了应用程序一致性备份，确保了还原数据时无需额外的修补程序。 还原应用程序一致型数据可减少还原时间，因此可快速恢复到运行状态。
- **进行短期和长期保留**：**长期保留** - 可以将恢复服务保管库用于短期和长期数据保留。 Azure 不会限制恢复服务保管库中数据的保留时间长度。 可以根据需要设置数据在保管库中的保留时间。 Azure 备份的限制为每个受保护实例仅限 9999 个恢复点。 请参阅本文的[备份和保留](backup-introduction-to-azure-backup.md#backup-and-retention)部分，了解此限制对用户备份需求的影响。
- **自动存储管理** - 混合环境常常需要异类存储（部分在本地，部分在云端）。 通过 Azure 备份，使用本地存储设备时无需付费。 Azure 备份会自动分配和管理备份存储，且采用即用即付模型。 即用即付是指只需为所用的存储付费。 有关详细详细，请参阅 [Azure 定价文章](https://azure.microsoft.com/pricing/details/backup)。
- **多个存储选项** - 高可用性的一个方面是存储复制。 Azure 备份提供两种类型的复制：[本地冗余存储](../storage/common/storage-redundancy-lrs.md)和[异地冗余存储](../storage/common/storage-redundancy-grs.md)。 根据需要选择备份存储选项：
    - 本地冗余存储 (LRS) 将数据中心的存储缩放单元中的数据复制三次（创建三个数据副本）。 数据的所有副本存在于同一区域。 LRS 是一种低成本选项，用于保护数据免受本地硬件故障的影响。
    - 异地冗余存储 (GRS) 是默认的和推荐的复制选项。 GRS 将数据复制到离源数据主位置数英里之外的次要区域中。 GRS 的成本比 LRS 的高，但 GRS 可让数据更为持久，即使出现区域性故障也是如此。


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Azure 备份与 Azure Site Recovery 的区别是什么？

Azure 备份和 Azure Site Recovery 服务有助于企业实现业务连续性和灾难恢复 (BCDR) 策略。 BCDR 包含两个主要目的：

- 在出现故障时确保业务数据的安全和可恢复性。
- 在计划内和计划外停机期间确保应用和工作负荷启动并运行。

两项服务都提供不同但互补的功能。

- **Azure Site Recovery**：Site Recovery 为本地计算机和 Azure VM 提供灾难恢复解决方案。 可以将计算机从主位置复制到辅助位置。 出现灾难时，可以将计算机故障转移到辅助位置，从辅助位置访问它们。 一切恢复正常后，可以对计算机执行故障回复，在主站点恢复它们。
- **Azure 备份**：Azure 备份服务可以从本地计算机和 Azure VM 备份数据。 可以在粒度级别备份和恢复数据，包括对文件、文件夹和计算机系统状态进行备份，以及进行应用感知型数据备份。 Azure 备份处理数据时所在的粒度级别比 Site Recovery 更细。 例如，如果便携式计算机上的演示文稿损坏，则可使用 Azure 备份来还原该演示文稿。 若要确保 VM 配置和数据的安全性和可访问性，则可使用 Site Recovery。  

以表格的形式列出 BCDR 需求。 

**目标** | **详细信息** | **比较**
--- | --- | --- | --- |
**数据备份/保留** | 可以根据符合性要求，将备份数据保留和存储数天、数月甚至数年。 | 可以通过 Azure 备份之类的备份解决方案细致地选取要备份的数据，并对备份和保留策略进行优化。<br/><br/> Site Recovery 不允许这样细致的优化。
**恢复点目标 (RPO)** | 在需要执行恢复的情况下可接受的数据丢失量。 | 备份的 RPO 的可变性更高。<br/><br/> VM 备份的 RPO 通常为一天，而数据库备份的 RPO 可以低至 15 分钟。<br/><br/> Site Recovery 提供的 RPO 低是因为复制持续且频繁，因此源和副本之间的差异小。
**恢复时间目标 (RTO)** |完成恢复或还原所需的时间量。 | 由于 RPO 较大，备份解决方案需要处理的数据量通常更多，这会导致 RTO 较长。 例如，根据从异地转送磁带所需的时间，从磁带还原数据可能需要数天的时间。 | 灾难恢复解决方案（例如 Site Recovery）的 RPO 低，因为持续/频繁复制通常意味着目标与源之间的同步频率更高。 |

## <a name="what-backup-scenarios-are-supported"></a>支持哪些备份方案？

Azure 备份可以备份本地计算机和 Azure VM。

**计算机** | **备份方案**
--- | ---
**本地计算机（物理/虚拟）** |  可以备份单个本地计算机。<br/><br/>可以备份受 System Center Data Protection Manager (DPM) 保护的本地计算机<br/><br/> 可以备份受 Microsoft Azure 备份服务器 (MABS) 保护的本地计算机。
**Azure VM** | 可以备份单个 Azure VM。<br/><br/> 可以备份受 DPM 或 MABS 保护的 Azure VM。

### <a name="back-up-servers"></a>备份服务器

可能需要备份本地服务器和工作负荷，或者备份 Azure VM 及其工作负荷，首先备份到备份服务器，然后备份到恢复服务保管库。 

**备份服务器** | **详细信息**
--- | ---
**System Center Data Protection Manager (DPM)** | 可以使用 Azure 备份来备份受 DPM 保护的数据：<br/><br/> - DPM 可以在本地计算机（物理机或虚拟机）上运行，也可以在 Azure 中运行。<br/><br/> - 可以对本地计算机和 Azure VM 上运行的不同类型的数据进行保护，方法是将数据备份到 DPM 服务器。<br/><br/> 反过来，也可使用 Azure 备份服务将 DPM 服务器备份到恢复服务保管库。<br/><br/> DPM 服务器及其保护的计算机必须位于同一网络中。 本地计算机只能受本地 DPM 服务器的保护。 同样，Azure VM 只能受 Azure 中运行的 DPM 的保护。
**Microsoft Azure 备份服务器 (MABS)** | 可以使用 Azure 备份来备份受 MABS 保护的数据：<br/><br/> - MABS 可以在本地计算机（物理机或虚拟机）上运行，也可以在 Azure 中运行。<br/><br/> - 可以对本地计算机和 Azure VM 上运行的不同类型的数据进行保护，方法是将数据备份到 MABS。<br/><br/> - 反过来，也可使用 Azure 备份服务将 MABS 备份到恢复服务保管库。<br/><br/> - MABS 提供与 DPM 类似的功能，只是不能备份到磁带。 MABS 不需要 System Center 许可证。<br/><br/> 与 DPM 一样，本地计算机只能受本地 MABS 的保护。 Azure VM 只能受 Azure 中的 MABS 的保护。

先备份到 DPM/MABS 再备份到保管库的优势如下：

- 备份到 DPM/MAB 时，可以在文件/文件夹/卷备份以及计算机状态备份（裸机、系统状态）的基础上提供应用感知型备份，后者已针对常用应用（例如 SQL Server、Exchange 和 SharePoint）进行优化。
- 不需在要备份的每台计算机上安装 Azure 备份代理。 每台计算机都运行 DPM/MABS 保护代理，而 Azure 备份或 Microsoft Azure 恢复服务代理只在 DPM 服务器/MABS 上运行。
- 有更多适合运行备份的灵活性和粒度计划选项。
- 可以管理多台计算机的备份，这些计算机已在单个控制台中集中到保护组。 当应用的分层跨越多台计算机，而你需要将它们一起进行备份时，此方法特别有用。

## <a name="what-can-be-backed-up"></a>可以备份什么内容？

**计算机** | **备份服务器** | **备份**
--- | --- | ---
本地 Windows VM | 不备份到 DPM 或 MABS | 备份文件、文件夹、系统状态。
Azure VM（Windows 和 Linux） | 不备份到 DPM 或 MABS | 备份文件、文件夹、系统状态。<br/><br/> Windows 计算机的备份为应用感知型，Linux 计算机的备份为文件感知型。
本地 VM/Azure VM | 受 DPM 保护 | 备份受 DPM 保护的任何内容，包括文件/文件夹/共享/卷以及特定于应用的数据。 [了解](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) DPM 可以进行哪些备份。
本地 VM/Azure VM | 受 MABS 保护 | 备份受 MABS 保护的任何内容，包括文件/文件夹/共享/卷以及特定于应用的数据。 [了解](backup-mabs-protection-matrix.md) MABS 可以进行哪些备份。

## <a name="what-backup-agents-do-i-need"></a>我需要什么样的备份代理？
**方案** | **代理** | **详细信息**
--- | --- | ---
本地计算机（无备份服务器） | Microsoft Azure 恢复服务 (MARS) 代理在 Windows 计算机上运行。 | 在 Azure 备份部署期间下载并安装 MARS 代理。<br/><br/> 仅对 Windows 计算机提供支持。
Azure VM（无备份服务器） | 不需显式代理。 用于备份的 Azure VM 扩展在 Azure VM 上运行。 | 此扩展在你运行第一个 Azure VM 备份时安装。<br/><br/> 提供 Windows 和 Linux 支持。
受 DPM 保护的本地计算机/Azure VM。 | MARS 代理在 DPM 服务器上运行。 | 不需要单台计算机上的 MARS 代理。<br/><br/> 部署 DPM 时，DPM 保护代理安装在你所保护的每台计算机上。 
备份受 MABS 保护的本地计算机和 Azure VM | MARS 代理在 MABS 上运行。 | 不需要单台计算机上的 MARS 代理。<br/><br/> 部署 MABS 时，MABS 保护代理安装在你所保护的每台计算机上。 


## <a name="which-backup-agent-should-i-use"></a>我应该使用哪个备份代理？

**备份** | **解决方案** | **限制**
--- | --- | ---
我要备份本地 Windows 计算机。 计算机不受 DPM 或 MABS 保护 | 在计算机上安装 MARS 代理。 | 可以将文件、文件夹和系统状态备份到 Azure。 备份不是应用感知型。
我要备份本地 Linux 计算机。 计算机不受 DPM 或 MABS 保护。 | 需先部署 DPM 或 MABS，然后才能将内容备份到 Azure。
我要备份在本地 Windows 计算机上运行的应用 | 若要进行应用感知型备份，Windows 计算机必须受 DPM 或 MABS 保护。
我要备份 Azure VM | 使用 Azure 备份来运行备份。 备份扩展会在 Windows 或 Linux Azure VM 上自动配置。 | 将会备份 VM 磁盘。<br/><br/> 就 Windows VM 来说，备份为应用一致性备份。 就 Linux 来说，备份为文件一致性备份。 如果需要应用感知型备份，则需使用自定义脚本进行此方面的配置。
我希望在备份 Azure VM 时，能够在备份和恢复设置方面保持粒度级别的灵活性 | 使用在 Azure 中运行的 DPM 或 MABS 来保护 Azure VM，这样可以在备份计划方面获得更多的灵活性，在保护和还原文件、文件夹、卷、应用和系统状态方面获得全部灵活性。


## <a name="next-steps"></a>后续步骤

- [查看](backup-architecture.md)不同备份方案的体系结构和组件。
- [验证](backup-support-matrix.md)备份的支持功能和设置。

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png


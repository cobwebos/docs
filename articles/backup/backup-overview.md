---
title: 什么是 Azure 备份？
description: 概述 Azure 备份服务及其如何有助于实现业务连续性和灾难恢复 (BCDR) 策略。
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: cf48090b2c32f0c3a1c8170873cb8d6a771fe21f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397068"
---
# <a name="what-is-the-azure-backup-service"></a>什么是 Azure 备份服务？

Azure 备份服务提供简单、安全且经济高效的解决方案来备份数据，并从 Microsoft Azure 云恢复数据。

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>可以备份哪些内容？

- **本地** - 使用 [Microsoft Azure 恢复服务 (MARS) 代理](backup-support-matrix-mars-agent.md)备份文件、文件夹和系统状态。 或者，使用 DPM 或 Azure 备份服务器 (MABS) 代理保护本地 VM（[Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) 和 [VMWare](backup-azure-backup-server-vmware.md)）及其他[本地工作负荷](backup-mabs-protection-matrix.md)
- **Azure VM** - [备份整个 Windows/Linux VM](backup-azure-vms-introduction.md)（使用备份扩展），或使用 [MARS 代理](backup-azure-manage-mars.md)备份文件、文件夹和系统状态。
- **Azure 文件存储共享** - [将 Azure 文件存储共享备份到存储帐户](backup-afs.md)
- **Azure VM 中的 SQL Server** -  [备份 Azure VM 上运行的 SQL Server 数据库](backup-azure-sql-database.md)
- **Azure VM 中的 SAP HANA 数据库** - [备份 Azure VM 上运行的 SAP HANA 数据库](backup-azure-sap-hana-database.md)

![Azure 备份概述](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>为何使用 Azure 备份？

Azure 备份具有以下主要优势：

- **卸载本地备份**： Azure 备份提供了一个简单的解决方案，用于将本地资源备份到云中。 获取短期和长期备份，不需部署复杂的本地备份解决方案。
- **备份 Azure IaaS vm**： azure 备份提供独立且独立的备份，以防止意外销毁原始数据。 备份存储在提供恢复点内置管理的恢复服务保管库中。 配置和可伸缩性很简单，备份经过优化，可以轻松地根据需要还原。
- **轻松缩放** - Azure 备份利用 Azure 云的基础功能和无限缩放功能实现高可用性 - 无需维护，也无需监视开销。
- **获取不受限制的数据传输**： Azure 备份不会限制传输的入站或出站数据量，也不会对传输的数据收费。
  - 出站数据是指还原操作期间从恢复服务保管库传输的数据。
  - 如果使用 Azure 导入/导出服务执行脱机初始备份以导入大量数据，则入站数据将产生相关费用。  [了解详细信息](backup-azure-backup-import-export.md)。
- 保护**数据安全**： Azure 备份提供用于保护[传输中](backup-azure-security-feature.md)的数据和[静态](backup-azure-security-feature-cloud.md)数据的解决方案。
- **集中监视和管理**： Azure 备份在恢复服务保管库中提供[内置的监视和警报功能](backup-azure-monitoring-built-in-monitor.md)。 无需任何附加的管理基础结构即可使用这些功能。 还可以[使用 Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) 提高监视和报告的规模。
- **获取应用一致**的备份：应用程序一致性备份意味着恢复点具有还原备份副本所需的所有数据。 Azure 备份提供应用程序一致性备份，确保无需进行额外的修复就能还原数据。 还原应用程序一致型数据可减少还原时间，因此可快速恢复到运行状态。
- **保留短期和长期数据**：可以使用[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)进行短期和长期数据保留。
- **自动存储管理** - 混合环境常常需要异类存储（部分在本地，部分在云端）。 在 Azure 备份中，使用本地存储设备无需付费。 Azure 备份会自动分配和管理备份存储，且采用即用即付模型。 因此，只需为所用的存储付费。 [详细了解](https://azure.microsoft.com/pricing/details/backup)定价情况。
- **多个存储选项** - Azure 备份提供两种类型的复制来保持存储/数据的高可用性。
  - [本地冗余存储 (LRS)](../storage/common/storage-redundancy-lrs.md) 将数据中心的存储缩放单元中的数据复制三次（创建三个数据副本）。 数据的所有副本存在于同一区域。 LRS 是一种低成本选项，用于保护数据免受本地硬件故障的影响。
  - [异地冗余存储 (GRS)](../storage/common/storage-redundancy-grs.md) 是默认的和推荐的复制选项。 GRS 将数据复制到离源数据主位置数英里之外的次要区域中。 GRS 的成本比 LRS 的高，但 GRS 提供更高的数据持久度，即使出现区域性服务中断也是如此。

## <a name="next-steps"></a>后续步骤

- [查看](backup-architecture.md)不同备份方案的体系结构和组件。
- [验证](backup-support-matrix.md)对备份和 [Azure VM 备份](backup-support-matrix-iaas.md)的支持要求和限制。

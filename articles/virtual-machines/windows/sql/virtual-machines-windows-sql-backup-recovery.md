---
title: Azure VM 中 SQL Server 的备份和还原 | Microsoft Docs
description: 介绍 Azure 虚拟机上运行的 SQL Server 数据库的备份和还原注意事项。
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: d46c55f809d24529ea5deeb4d84de44dae876a4b
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968980"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Azure 虚拟机中 SQL Server 的备份和还原

本文提供有关适用于 Windows Azure 虚拟机中运行的 SQL Server 的备份和还原选项的指导。 Azure 存储维护每个 Azure VM 磁盘的三个副本，以确保数据不会丢失或物理数据不会损坏。 因此，与在本地不同，无需重点关注硬件故障问题。 但是，仍应备份 SQL Server 数据库，以防止发生应用程序或用户错误，例如意外的数据插入或删除。 如果出现这种意外，必须能够还原到特定的时间点。

本文的第一部分提供可用备份和还原选项的概述。 后续部分提供有关每种策略的详细信息。

## <a name="backup-and-restore-options"></a>备份和还原选项

下表提供有关适用于 Azure VM 上运行的 SQL Server 的各种备份和还原选项的信息：

| 策略 | SQL 版本 | 说明 |
|---|---|---|---|
| [自动备份](#automated) | 2014<br/> 2016<br/> 2017 | 使用自动备份可以针对 SQL Server VM 上的所有数据库计划定期备份。 备份在 Azure 存储中最多存储 30 天。 从 SQL Server 2016 开始，自动备份 v2 提供更多选项，例如，配置手动计划，以及完整备份和日志备份的频率。 |
| [适用于 SQL VM 的 Azure 备份](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | Azure 备份为 Azure VM 中运行的 SQL Server 提供企业级备份功能。 使用此服务，可以集中管理多个服务器和数千个数据库的备份。 可在门户中将数据库还原到特定的时间点。 此服务提供可将备份保留数年之久的可自定义保留策略。 此功能目前处于公开预览状态。 |
| [手动备份](#manual) | 全部 | 根据所用的 SQL Server 版本，可通过不同的方法手动备份和还原 Azure VM 上运行的 SQL Server。 在这种情况下，你需要负责指定数据库的备份方式和存储位置，并管理这些备份。 |

以下部分更详细地介绍了每个选项。 本文的最后一个部分以功能矩阵的形式提供了摘要。

## <a id="autoamted"></a>自动备份

自动备份为 Windows Azure VM 中运行的 SQL Server Standard 和 Enterprise 版本提供自动备份服务。 此服务由 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)提供。该扩展已自动安装在 Azure 门户中的 SQL Server Windows 虚拟机映像上。

所有数据库将备份到配置的 Azure 存储帐户中。 备份可以加密，最多会保留 30 天。

SQL Server 2016 和更高版本的 VM 提供更多的自定义选项，以及自动备份 v2。 这些改进包括：

- 系统数据库备份
- 手动备份计划和时间窗口
- 完整备份和日志文件备份的频率

若要还原数据库，必须在存储帐户中找到所需的备份文件，并使用 SQL Server Management Studio (SSMS) 或 Transact-SQL 命令对 SQL VM 执行还原。

有关如何为 SQL VM 配置自动备份的详细信息，请参阅以下文章之一：

- **SQL Server 2016/2017**：[适用于 Azure 虚拟机的自动备份 v2](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**：[适用于 SQL Server 2014 虚拟机的自动备份](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a>适用于 SQL VM 的 Azure 备份（公共预览版）

[Azure 备份](/azure/backup/)为 Azure VM 中运行的 SQL Server 提供企业级备份功能。 在恢复服务保管库中存储和管理所有备份。 此解决方案提供许多优势，尤其是针对企业：

- **零基础结构备份**：无需管理备份服务器或存储位置。
- **规模**：保护大量的 SQL VM 和数千个数据库。
- **即用即付**：此功能是 Azure 备份提供的独立服务，但与所有 Azure 服务，你只需为使用的功能付费。
- **集中式管理和监视**：通过 Azure 中的单个仪表板集中管理所有备份，包括 Azure 备份支持的其他工作负荷。
- **策略驱动的备份和保留**：为定期备份创建标准备份策略。 建立保留策略，将备份保留数年之久。
- **支持 SQL Always On**：检测和保护 SQL Server Always On 配置，并遵循备份可用性组的备份首选项。
- **15 分钟恢复点目标 (RPO)**：最多可将 SQL 事务日志备份频率配置为每隔 15 分钟备份一次。
- **时间点还原**：使用门户将数据库恢复到特定的时间点，无需手动还原多个完整备份、差异备份和日志备份。
- **合并的故障电子邮件警报**：针对任何故障配置合并的电子邮件通知。
- **基于角色的访问控制**：确定谁可以通过门户管理备份和还原操作。

有关此解决方案工作原理的简要概述和演示，请观看以下视频：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2dNbw]

适用于 SQL VM 的此 Azure 备份解决方案目前以公共预览版提供。 有关详细信息，请参阅[将 SQL Server 数据库备份到 Azure](../../../backup/backup-azure-sql-database.md)。

## <a id="manual"></a>手动备份

若要手动管理 SQL VM 上的备份和还原操作，可以根据所用的 SQL Server 版本使用多个选项。 有关备份和还原的概述，请根据所用的 SQL Server 版本参阅以下文章之一：

- [适用于 SQL Server 2016 和更高版本的备份和还原](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [适用于 SQL Server 2014 的备份和还原](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [适用于 SQL Server 2012 的备份和还原](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [适用于 SQL Server 2008 R2 的备份和还原](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [适用于 SQL Server 2008 的备份和还原](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

以下部分更详细地介绍多个手动备份和还原选项。

### <a name="backup-to-attached-disks"></a>备份到附加的磁盘

对于 Azure VM 中运行的 SQL Server，可以使用 VM 上附加的磁盘作为备份文件目标，通过本机备份和还原技术实现此目的。 不过，只能[根据虚拟机的大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，将有限数量的磁盘附加到 Azure 虚拟机。 磁盘管理开销也是一个考虑因素。

有关如何使用 SQL Server Management Studio (SSMS) 或 Transact-SQL 手动创建完整数据库备份的示例，请参阅[创建完整数据库备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server)。

### <a name="backup-to-url"></a>备份到 URL

从 SQL Server 2012 SP1 CU2 开始，可以直接备份和还原到 Microsoft Azure Blob 存储，此过程也称为备份到 URL。 SQL Server 2016 还对此功能做出了以下增强：

| 2016 增强功能 | 详细信息 |
| --- | --- |
| **条带化** |备份到 Microsoft Azure Blob 存储时，SQL Server 2016 支持备份到多个 Blob，以便能够备份高达 12.8 TB 的大型数据库。 |
| **快照备份** |通过使用 Azure 快照，SQL Server 快照备份为使用 Azure Blob 存储服务中存储的数据库文件提供接近实时的备份和更快速的还原。 使用此功能可简化备份和还原策略。 文件快照备份还支持时间点还原。 有关详细信息，请参阅 [Azure 中针对数据库文件的快照备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)。 |

有关详细信息，请根据所用的 SQL Server 版本参阅以下文章之一：

- **SQL Server 2016/2017**：[将 SQL Server 备份到 URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**：[将 SQL Server 2014 备份到 URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**：[将 SQL Server 2012 备份到 URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>托管备份

从 SQL Server 2014 开始，托管备份会自动在 Azure 存储中创建备份。 在幕后，托管备份使用本文上一部分所述的“备份到 URL”功能。 托管备份也是支持 SQL Server VM 自动备份服务的基础功能。

从 SQL Server 2016 开始，托管备份添加了更多的选项用于配置计划、系统数据库备份，以及完整备份和日志备份的频率。

有关详细信息，请根据所用的 SQL Server 版本参阅以下文章之一：

- [在 Microsoft Azure 中对 SQL Server 2016 和更高版本进行托管备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [在 Microsoft Azure 中对 SQL Server 2014 进行托管备份](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>决策矩阵

下表汇总了 Azure 中 SQL Server 虚拟机的每个备份和还原选项的功能。

|| **自动备份** | **适用于 SQL 的 Azure 备份** | **手动备份** |
|---|---|---|---|
| 需要额外的 Azure 服务 |   | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 在 Azure 门户中配置备份策略 | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 在 Azure 门户中还原数据库 |   | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 在一个仪表板中管理多个服务器 |   | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 时间点还原 | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15 分钟恢复点目标 (RPO) | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 短期备份保留策略（天） | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 长期备份保留策略（月、年） |   | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 对 SQL Server Always On 的内置支持 |   | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 备份到 Azure 存储帐户 | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png)（自动） | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png)（自动） | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png)（由客户管理） |
| 存储和备份文件的管理 | | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| 备份到 VM 上附加的磁盘 |   |   | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 集中式可自定义备份报告 |   | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 合并的故障电子邮件警报 |   | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 基于 OMS 自定义监视 |   | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 使用 SSMS 或 Transact-SQL 脚本监视备份作业 | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 使用 SSMS 或 Transact-SQL 脚本还原数据库 | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![是](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>后续步骤

规划 Azure VM 中的 SQL Server 部署时，可在以下指南中找到预配指导：[如何在 Azure 门户中预配 Windows SQL Server 虚拟机](virtual-machines-windows-portal-sql-server-provision.md)。

尽管备份和还原可用于迁移数据，但是，Azure VM 上的 SQL Server 可能还存在更便捷的数据迁移路径。 有关迁移选项和建议的完整讨论，请参阅[将数据库迁移到 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)。
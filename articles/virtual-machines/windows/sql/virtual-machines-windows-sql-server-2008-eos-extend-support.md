---
title: 扩展对 SQL Server 2008 & 2008 R2 的支持
description: 通过将 SQL Server 实例迁移到 Azure 来扩展对 SQL Server 2008 和 SQL Server 2008 R2 的支持，或购买扩展支持以将实例保留在本地。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 7c93538982b7fd7bb2f5ac25027ed92cc6ccbfa3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357843"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>通过 Azure 扩展对 SQL Server 2008 和 SQL Server 2008 R2 的支持

SQL Server 2008 和 SQL Server 2008 R2 都已达到[其支持（EOS）生命周期的结束](https://www.microsoft.com/sql-server/sql-server-2008)时间。 由于许多客户仍在使用这两个版本，因此我们提供了几个选项来继续获得支持。 你可以将本地 SQL Server 实例迁移到 Azure 虚拟机（Vm）、迁移到 Azure SQL 数据库，或保留本地并购买扩展的安全更新。

与托管实例不同，迁移到 Azure VM 不需要 recertifying 你的应用程序。 与保持本地不同，你会通过迁移到 Azure VM 来接收免费的扩展安全修补程序。

本文的其余部分提供了有关将 SQL Server 实例迁移到 Azure VM 的注意事项。

有关支持选项结束的详细信息，请参阅[支持结束](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)。

## <a name="provisioning"></a>提供

Azure Marketplace 上提供了**Windows Server 2008 r2 映像上**的即用即付 SQL Server 2008 r2。

SQL Server 2008 的客户需要自行安装或升级到 SQL Server 2008 R2。 同样，Windows Server 2008 上的客户需要从自定义 VHD 或升级到 Windows Server 2008 R2 部署其 VM。

通过 Azure Marketplace 部署的映像附带预装的 SQL IaaS 扩展。 SQL IaaS 扩展是灵活的许可和自动修补的要求。 部署自行安装的 Vm 的客户需要手动安装 SQL IaaS 扩展。 Windows Server 2008 不支持 SQL IaaS 扩展。

> [!NOTE]
> 尽管 SQL Server "**创建**和**管理**" 边栏选项卡将适用于 Azure 门户中的 SQL Server 2008 R2 映像，但_不支持_以下功能：自动备份、Azure Key Vault 集成、R Services 和存储配置。

## <a name="licensing"></a>许可
即用即付 SQL Server 2008 R2 部署可以转换为[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)。

若要将基于软件保障（SA）的许可证转换为即用即付许可证，客户应该向 SQL VM[资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)注册。 完成该注册后，SQL 许可证类型在 Azure 混合权益与即用即付之间可以互换。

在 Azure VM 上自行安装的 SQL Server 2008 或 SQL Server 2008 R2 实例可以注册 SQL VM 资源提供程序，并将其许可证类型转换为即用即付。

## <a name="migration"></a>迁移
可以使用手动备份/还原方法将 EOS SQL Server 实例迁移到 Azure VM。 这是从本地到 Azure VM 的最常见迁移方法。

### <a name="azure-site-recovery"></a>Azure 站点恢复

对于大容量迁移，建议[Azure Site Recovery](/azure/site-recovery/site-recovery-overview)服务。 通过 Azure Site Recovery，客户可以复制整个 VM，包括从本地到 Azure VM SQL Server。

SQL Server 要求应用一致 Azure Site Recovery 快照以保证恢复。 Azure Site Recovery 支持最少1小时间隔的应用一致性快照。 Azure Site Recovery 迁移的 SQL Server 可能的最低恢复点目标（RPO）为1小时。 恢复时间目标（RTO）是2小时加上 SQL Server 恢复时间。

### <a name="database-migration-service"></a>数据库迁移服务

如果客户从本地迁移到 Azure VM，则可以通过将 SQL Server 升级到2012版本或更高版本，使用[数据库迁移服务](/azure/dms/dms-overview)。

## <a name="disaster-recovery"></a>灾难恢复

Azure VM 上的 EOS SQL Server 的灾难恢复解决方案如下所示：

- **SQL Server 备份**：使用 Azure 备份来帮助保护你的 EOS SQL Server 免受勒索软件、意外删除和损坏。 此解决方案目前处于对 EOS SQL Server 的预览中，并支持在 Windows 2008 R2 SP1 上运行 SQL Server 2008 和 2008 R2。 有关更多详细信息，请参阅[此文](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support)。
- **日志传送**：你可以在另一个区域或 Azure 区域中创建一个日志传送副本，其中包含连续还原以减少 RTO。 需要手动配置日志传送。
- **Azure Site Recovery**：可以通过 Azure Site Recovery 复制在区域和区域之间复制 VM。 SQL Server 需要使用应用一致的快照来保证在发生灾难时进行恢复。 Azure Site Recovery 为 EOS SQL Server 灾难恢复提供至少1小时的 RPO 和2小时（外加 SQL Server 恢复时间） RTO。

## <a name="security-patching"></a>安全修补
在将 SQL Server VM 注册到 SQL VM[资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)后，通过 Microsoft 更新通道传递 SQL Server vm 的扩展安全更新。 可以手动或自动下载修补程序。

*自动修补* ：默认处于启用状态。 Azure 可以通过自动修补来自动修补 SQL Server 和操作系统。 如果安装了 SQL Server IaaS 扩展，则可以指定维护时段的周日期、时间和持续时间。 Azure 会在维护时段进行修补。 维护时段计划使用 VM 的时间区域设置。  有关详细信息，请参阅[Azure 虚拟机上 SQL Server 的自动修补](virtual-machines-windows-sql-automated-patching.md)。


## <a name="next-steps"></a>后续步骤

将 SQL Server VM 迁移到 Azure：

* [将 SQL Server 数据库迁移到 Azure VM 中的 SQL Server](virtual-machines-windows-migrate-sql.md)

Azure 虚拟机上的 SQL Server 入门：

* [在 Azure 门户中创建 SQL Server VM](quickstart-sql-vm-create-portal.md)

获取有关 SQL Server Vm 的常见问题的解答：

* [Azure 虚拟机上的 SQL Server 常见问题](virtual-machines-windows-sql-server-iaas-faq.md)

详细了解支持选项和扩展安全更新：

*  & [扩展安全更新](/sql/sql-server/end-of-support/sql-server-extended-security-updates)[的支持结束](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)
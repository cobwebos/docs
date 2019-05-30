---
title: 扩展对 SQL Server 2008 和 SQL Server 2008 R2 与 Azure 支持
description: 了解如何通过将您的 SQL Server 实例迁移到 Azure，或在购买保留实例本地的扩展的支持扩展对 SQL Server 2008 和 SQL Server 2008 R2 的支持。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 62261e46dc4744597acd10c32f0a835f4a597d4d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243977"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>扩展对 SQL Server 2008 和 SQL Server 2008 R2 与 Azure 支持

SQL Server 2008 和 SQL Server 2008 R2 都接近[其支持 (EOS) 生命周期结束](https://www.microsoft.com/sql-server/sql-server-2008)。 由于我们的许多客户仍在使用这两个版本，我们提供了几个选项以继续获取支持。 可以将你的本地 SQL Server 实例迁移到 Azure 虚拟机 (Vm)，将迁移到 Azure SQL 数据库，或仍保留在本地并购买扩展的安全更新。

与不同的托管实例迁移到 Azure VM 不需要 recertifying 您的应用程序。 和不同于通过保持在本地，您将收到免费的扩展的安全修补程序通过将迁移到 Azure VM。 

本文的其余部分提供了有关将 SQL Server 实例迁移到 Azure VM 的注意事项。 

## <a name="provisioning"></a>设置 

没有现用现付`SQL Server 2008 R2 on Windows Server 2008 R2`Azure marketplace 上提供的映像。 

SQL Server 2008 上的客户将需要自行安装或升级到 SQL Server 2008 R2。 同样，Windows Server 2008 上的客户将需要将自定义 VHD 从其 VM 部署或升级到 Windows Server 2008 R2。 

通过 Marketplace 部署的映像附带了预安装了 SQL IaaS 扩展。 SQL IaaS 扩展是必需的灵活的许可和自动修补。 部署自已安装的 Vm 的客户将需要手动安装 SQL IaaS 扩展。 在 Windows 2008 上不支持的 SQL IaaS 扩展。 

  > [!NOTE]
  > 尽管 SQL Server`Create`并`Manage`边栏选项卡将使用 Azure 门户中的 SQL Server 2008R2 映像，以下功能_不支持_:自动备份、 Azure 密钥保管库集成、 R Services 和存储配置。

## <a name="licensing"></a>许可
可以转换为即用即付 SQL Server 2008R2 部署[Azure 混合权益 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/)。

若要将软件保障 (SA) 基于许可证转换为即用即付，客户应注册与 SQL VM[资源提供程序](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)。 后注册到 SQL VM 资源提供程序，SQL 许可证类型将是可互换 AHB 和即用即付之间。 

Azure VM 上自行安装的 SQL Server 2008 或 SQL Server 2008 R2 实例可以使用 SQL 资源提供程序注册，并将他们的许可类型转换为即用即付。

## <a name="migration"></a>迁移
你可以使用手动备份/还原方法; 到 Azure VM 迁移 EOS SQL Server 实例这是从本地到 Azure VM 的最常见迁移方法。

### <a name="azure-site-recovery"></a>Azure Site Recovery

对于大容量迁移，我们建议[Azure Site Recovery](/azure/site-recovery/site-recovery-overview)服务。 使用 Azure Site Recovery，客户可以将整个 VM 包括 SQL Server 从本地到 Azure VM 复制。

SQL Server 需要应用程序一致的 Azure Site Recovery 快照，以保证恢复;和 Azure Site Recovery 支持最小 1 小时间隔中使用的应用一致性快照。 使用 Azure Site Recovery 迁移可能适用于 SQL Server 的最小 RPO 为 1 小时和 RTO 是 2 个小时加上 SQL Server 恢复时间。

### <a name="database-migration-service"></a>数据库迁移服务

[数据库迁移服务](/azure/dms/dms-overview)如果从本地迁移到 Azure VM 的 SQL Server 升级到 SQL Server 2012 及更高版本的客户是一个选项。

## <a name="disaster-recovery"></a>灾难恢复

EOS 上的 SQL Server Azure VM 灾难恢复解决方案如下所示：

- **SQL Server 备份**:可以使用 SQL Server 备份发生区域时的 SQL Server 或区域故障中恢复。 由于 EOS SQL Server 不支持托管备份功能，客户将需要手动进行备份。
- **日志传送**:可以创建日志传送副本在另一个区域或连续地恢复来减少 RTO 的 Azure 区域。 客户将需要手动配置日志传送。
- **Azure Site Recovery**：可以区域和通过 Azure Site Recovery 复制的区域之间复制 VM。 SQL Server 要求应用程序一致的快照，以确保在发生灾难时进行恢复。 Azure Site Recovery 提供最小 1 小时的 RPO 和 2 小时 + SQL Server 恢复时间 RTO EOS SQL Server 灾难恢复。

## <a name="security-patching"></a>安全修补
带有 SQL 注册 SQL Server 虚拟机后，将通过 Microsoft 更新通道传递出去的 SQL Server Vm 的扩展的安全更新[资源提供程序](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)。 也可以手动或自动下载修补程序。 

**自动修补** ：默认处于启用状态。 Azure 可以通过自动修补来自动修补 SQL Server 和操作系统。 如果安装 SQL IaaS 扩展，可以指定一周、 时间和维护时段的持续时间的日。 Azure 会在维护时段进行修补。 维护时段计划使用 VM 的时间区域设置。  有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动修补](virtual-machines-windows-sql-automated-patching.md)。


## <a name="next-steps"></a>后续步骤

将 SQL Server VM 迁移到 Azure

* [将 SQL Server 数据库迁移到 Azure VM 中的 SQL Server](virtual-machines-windows-migrate-sql.md)

Azure 虚拟机上的 SQL Server 入门：

* [在 Azure 门户中创建 SQL Server VM](quickstart-sql-vm-create-portal.md)

获取有关 SQL VM 的常见问题的解答：

* [Azure 虚拟机中的 SQL Server 常见问题](virtual-machines-windows-sql-server-iaas-faq.md)

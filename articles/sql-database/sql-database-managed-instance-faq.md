---
title: SQL 数据库托管实例常见问题 |Microsoft Docs
description: SQL 数据库托管的实例方面的常见问题 (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/08/2019
ms.openlocfilehash: c3a070eb7e1435055b47b39985cf8cb0b182a514
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798068"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL 数据库托管的实例方面的常见问题 (FAQ)

本文包含许多最常见的问题有关[SQL 数据库托管实例](sql-database-managed-instance.md)。

## <a name="where-can-i-find-a-list-of-features-that-are-supported-on-managed-instance"></a>在哪里可以找到托管实例支持的功能的列表？

托管实例中支持的功能的列表，请参阅[Azure SQL 数据库与 SQL Server](sql-database-features.md)。

Azure SQL 数据库托管实例和本地 SQL Server 之间的语法和行为差异，请参阅[从 SQL Server 的 T-SQL 差异](sql-database-managed-instance-transact-sql-information.md)。


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>托管实例，在哪里可以找到技术特性和资源限制？

有关可用的硬件生成特征，请参阅[代次的硬件技术之间的差异](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。
有关可用服务层和它们的特征，请参阅[技术服务层之间的差异](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

## <a name="where-can-i-find-known-issues-and-bugs"></a>在哪里可以找到已知的问题和 bug？

Bug 和已知的问题，请参阅[行为的更改](sql-database-managed-instance-transact-sql-information.md#Changes)并[已知问题](sql-database-managed-instance-transact-sql-information.md#Issues)。


## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>托管的实例可以具有与在本地 SQL Server 相同的名称？

托管的实例必须具有名称的末尾*database.windows.net*。 而不是默认值，例如，使用另一个 DNS 区域**mi 另一个名称**。 contoso.com: 
- 使用 CliConfig 定义别名 (该工具只是注册表设置包装，因此这也可以使用组策略或脚本)。
- 使用*CNAME*与*TrustServerCertificate = true*选项。


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>如何将数据库从托管实例返回到 SQL Server 或 Azure SQL 数据库？

你可以[将数据库导出到 bacpac](sql-database-export.md) ，然后[bacpac 文件导入]( sql-database-import.md)。 如果小于 100 GB 数据库，则，建议使用此方法。

如果在数据库中的所有表都有主键，可以使用事务复制。

本机`COPY_ONLY`从托管实例创建的备份无法还原到 SQL Server，因为托管的实例相比于 SQL Server 的更高版本数据库版本。

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>如何将我的实例数据库迁移到单个 Azure SQL 数据库？

一种方法是向[将数据库导出到 bacpac](sql-database-export.md) ，然后[bacpac 文件导入]( sql-database-import.md)。 

如果数据库小于 100 GB，这是推荐的方法。 如果在数据库中的所有表都有主键，可以使用事务复制。

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>如何选择第 4 代和第 5 代硬件代次进行托管实例之间？

这取决于你的工作负荷更适合某些类型的工作负荷比另一些硬件代次原样。 而是一个复杂来简化性能主题时，以下差异影响工作负荷性能的硬件代之间：
- 第 4 代提供更好的计算支持，因为它基于物理处理器，而不是基于 vCore 的处理器上的第 5 代。 这可能是针对计算密集型工作负载更有利。
- 第 5 代支持加速网络从而导致更好的 IO 带宽到远程存储。 这可能是常规用途服务层上的 IO 密集型工作负荷具有明显的优势。 第 5 代使用与第 4 代相比更快 SSD 本地磁盘。 这可能是业务关键服务层上的 IO 密集型工作负荷具有明显的优势。

建议客户测试性能的适用于在之前投入生产的实际工作负荷以确定哪些硬件代将更好地在此示例中。

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>可以切换我托管的实例硬件代次之间第 4 代和第 5 代联机吗？ 

自动联机之间的切换代次的硬件是当这两个硬件代次是在其中预配你的托管的实例的同一区域中可用。 在这种情况下，你可以选择在 Azure 门户代次的硬件之间进行切换的定价层部分。

这是长时间运行操作，如在后端和数据库将会自动设置新的托管的实例的旧的和新的实例之间传输。 此过程将无缝的客户。

如果这两个代次的硬件不支持在同一区域中，更改硬件代次可以但必须手动完成。 这需要你预配的区域中的新实例所需的硬件代次是可用，并手动备份和还原的旧的和新的实例之间的数据。


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>如何优化我的托管实例的性能？ 

托管的实例常规用途使用远程存储由于数据和日志文件的大小对于性能很重要。 若要优化常规用途服务层性能，请按照此博客文章中的说明。

对于 IO 密集型工作负荷，请考虑使用第 5 代硬件，而不是使用第 4 代为计算密集型工作负荷。 有关详细信息，在不同代硬件供客户进行选择，请参阅 FAQ 部分。

如果你的工作负荷包括多个小事务，请考虑切换代理重定向模式中的连接类型。

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>什么是托管实例的最大存储大小？ 

托管实例的存储大小取决于所选的服务层 （常规用途或业务关键）。 有关这些服务层的存储限制，请参阅[服务层特征](sql-database-service-tiers-general-purpose-business-critical.md)。

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>从我的托管的实例存储中减去的备份存储吗？ 

不能，备份存储不会从你托管的实例的存储空间。 备份存储，将独立于实例的存储空间和不受限制的大小。 备份存储受你实例的数据库，可配置从 7 到 35 天的备份保留的时间。 有关详细信息，请参阅[自动备份](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>如何管理端口上设置入站的 NSG 规则？

内置的防火墙功能允许从仅供 Microsoft 管理/部署计算机和有效地阻止的安全管理工作站相关联的 IP 范围的入站的连接在群集中的所有虚拟机上配置 Windows 防火墙入侵，通过网络层。

下面是有关使用哪些端口：

使用 Service Fabric 基础结构的端口 9000 和 9003。exchange。 Service Fabric 的主要作用是使虚拟群集保持正常运行并保留在组件副本数方面的目标状态。

端口 1438年、 1440 和 1452年代理所使用的节点。 节点代理是在群集中运行并控制平面用于执行管理命令的应用程序。

除了内置的网络层上的防火墙，还受证书保护的通信。
  
有关详细信息以及如何验证内置防火墙，请参阅[Azure SQL 数据库托管实例内置防火墙](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。


## <a name="how-can-i-mitigate-networking-risks"></a>如何消除网络风险？ 

若要缓解任何网络风险，建议客户将安全设置和控件的一组应用：

- 所有数据库都启用透明数据加密 (TDE)。
- 启用公共语言运行时 (CLR) 关闭。 这被建议在本地以及。
- 使用仅限 Azure AD 帐户。
- 使用低特权的数据库管理员帐户访问 SQL MI。
- 配置系统管理员帐户的 JiT jumpbox 访问权限。
- 启用 SQL 审核，并将其与警报机制集成。
- 从 ATS 套件启用威胁检测。


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>与托管实例，在哪里可以找到用例和产生的成本节约？

托管的实例案例研究：

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
若要获取更好地了解优势、 成本和风险与部署 Azure SQL 数据库托管的实例相关联，还有 Forrester 的研究：[MI 的总体经济影响](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。


## <a name="can-i-do-dns-refresh"></a>可以执行 DNS 刷新？ 
  
目前，我们没有提供一项功能来刷新 DNS 服务器配置为托管实例。

最终刷新 DNS 配置：

- 当 DHCP 租约过期。
- 平台升级。

解决方法是，降级到 4 个 vCore 的托管的实例和之后再次对其进行升级。 这有副作用的刷新 DNS 配置。


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>托管的实例可以使用静态 IP 地址？

在罕见但非常必要的情况下，我们需要执行的联机迁移到新的虚拟群集的托管实例。 如果需要此迁移是因为我们旨在提高安全性和可靠性的在服务的技术堆栈中的更改。 迁移到新的虚拟群集结果中更改映射到托管的实例主机名的 IP 地址。 托管的实例服务不声明静态 IP 地址支持，并保留权利，恕不另行通知作为常规维护周期的一部分。

出于此原因，我们强烈建议不要依赖于 IP 地址的不可变性，因为它可能会导致不必要的停机时间。


## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>可以更改现有的托管实例的时区？

第一次预配的托管的实例时，可以设置时区配置。 不支持更改现有的托管实例的时区。 有关详细信息，请参阅[时区限制](sql-database-managed-instance-timezone.md#limitations)。

解决方法包括创建新的托管的实例的正确的时区，然后可以执行手动备份和还原，或我们的建议，执行[跨实例时点还原](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)。


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>如何解决性能问题与我的托管实例

有关托管的实例和 SQL Server 之间的性能比较，是很好的起点[Azure SQL 托管实例和 SQL Server 之间的性能比较的最佳做法](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)。

数据加载速度通常较慢于的托管实例上 SQL Server 中由于强制完整恢复模式和[限制](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)对事务日志写入吞吐量。 有时，这可以得到解决暂时性数据加载到 tempdb，而不是用户数据库，或使用聚集列存储索引或内存优化表。


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>可以加密的数据库还原到托管实例？

是的不需要解密你的数据库能够将其还原到托管实例。 需要提供证书/密钥用作加密密钥保护程序到托管实例在源系统中要能够从加密的备份文件中读取数据。 有两种可行方法执行此操作：

- 将证书保护程序上载到托管实例。 这可以仅使用 PowerShell。 示例脚本介绍了整个过程。
- 将非对称密钥保护程序上传到 Azure Key Vault (AKV) 和点指向它的托管的实例。 此方法类似于将你的自己的密钥 (BYOK) TDE 用例还使用 AKV 集成来存储加密密钥。 如果只是想上传到可用于还原的加密的数据库，而无需实际使用密钥加密密钥保护程序作为托管实例使用 AKV 的密钥，请按照说明设置 BYOK 的 TDE，并不选中复选框使所选密钥默认的 TDE 保护程序。

之后您将加密保护程序提供给托管实例，就可以继续使用标准数据库的还原过程。

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>如何可以从迁移 Azure SQL 数据库单一或弹性池到托管实例？ 

托管实例产品/服务相同的性能级别每个计算和存储大小为 Azure SQL 数据库的其他部署选项。 如果你想要合并的单个实例上的数据，或者您只需以独占方式在托管实例所支持的功能，您可以通过使用导出/导入 (BACPAC) 功能将数据迁移。

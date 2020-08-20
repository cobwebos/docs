---
title: 常见问题 (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Azure SQL 托管实例常见问题解答 (FAQ)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: fe779ebf8bb041fb90b8eb38a9469a783127ffd3
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661412"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Azure SQL 托管实例常见问题解答 (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文包含有关 [Azure SQL 托管实例](sql-managed-instance-paas-overview.md)的最常见问题。

## <a name="supported-features"></a>支持的功能

**在何处可以找到 SQL 托管实例上受支持的功能列表？**

有关SQL 托管实例中受支持的功能列表，请参阅 [Azure SQL 托管实例功能](../database/features-comparison.md)。

有关 Azure SQL 托管实例与 SQL Server 之间的语法和行为差异，请参阅 [T-SQL 与 SQL Server 之间的差异](transact-sql-tsql-differences-sql-server.md)。


## <a name="technical-specification-resource-limits-and-other-limitations"></a>技术规范、资源限制和其他限制
 
**在何处可以找到 SQL 托管实例的技术特征和资源限制？**

有关可用硬件的代系特征，请参阅[硬件代系的技术差异](resource-limits.md#hardware-generation-characteristics)。
有关可用的服务层级及其特征，请参阅[服务层级之间的技术差异](resource-limits.md#service-tier-characteristics)。

**我有资格获得哪些服务层？**

任何客户都有资格获得任何服务层。 但是，如果你想要通过使用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)在 Azure SQL 托管实例上交换现有的折扣率，请记住，具有软件保障的 SQL Server Enterprise Edition 客户有资格使用 [常规用途](../database/service-tier-general-purpose.md) 或 [业务关键](../database/service-tier-business-critical.md) 的性能层，并且具有软件保障的 SQL Server Standard Edition 客户仅适用于常规用途性能层。 有关更多详细信息，请参阅 [AHB 的特定权限](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server)。

**SQL 托管实例支持哪些订阅类型？**

有关支持的订阅类型的列表，请参阅 [支持的订阅类型](resource-limits.md#supported-subscription-types)。 

**支持哪些 Azure 区域？**

可以在大多数 Azure 区域中创建托管实例;请参阅 [SQL 托管实例支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 如果你需要当前不受支持的区域中的托管实例，请 [通过 Azure 门户发送支持请求](../database/quota-increase-request.md)。

**SQL 托管实例部署是否有任何配额限制？**

托管实例有两个默认限制：限制你可以使用的子网数量，以及可预配的 Vcore 数的限制。 各个订阅类型和区域的限制各不相同。 有关按订阅类型列出的区域资源限制列表，请参阅 [区域资源限制](resource-limits.md#regional-resource-limitations)中的表。 这些是可以按需增加的软限制。 如果需要在当前区域中预配更多托管实例，则发送支持请求以使用 Azure 门户增加配额。 有关详细信息，请参阅 [AZURE SQL 数据库的请求配额增加](../database/quota-increase-request.md)。

**我是否可以根据需要增加托管实例上的数据库数限制 (100) ？**

否，当前没有已提交的计划来增加 SQL 托管实例上的数据库数。 

**如果我有超过8TB 的数据，可以迁移到何处？**
可以考虑迁移到符合工作负荷的其他 Azure 风格： [AZURE SQL 数据库超大规模](../database/service-tier-hyperscale.md) 或 [azure 虚拟机上的 SQL Server](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)。

**如果我有特定的硬件要求（如更大的 RAM）到 vCore 或更多 Cpu，可以迁移到何处？**
可以考虑迁移到 Azure 虚拟机或[AZURE SQL 数据库](../database/sql-database-paas-overview.md)内存/cpu[上的 SQL Server](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 。

## <a name="known-issues-and-defects"></a>已知问题和缺陷

**在哪里可以找到已知问题和缺陷？**

有关产品缺陷和已知问题，请参阅 [已知问题](../database/doc-changes-updates-release-notes.md#known-issues)。

## <a name="new-features"></a>新增功能

**在何处可以找到最新功能和公共预览版功能？**

对于新功能和预览功能，请参阅[发行说明](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)。

## <a name="create-update-delete-or-move-sql-managed-instance"></a>创建、更新、删除或移动 SQL 托管实例

**如何预配 SQL 托管实例？**

可以通过 [Azure 门户](instance-create-quickstart.md)、 [PowerShell](scripts/create-configure-managed-instance-powershell.md)、 [Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) 和 [ARM 模板](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates)设置实例。

**能否在现有订阅中预配托管实例？**

是的，如果订阅属于所 [支持的订阅类型](resource-limits.md#supported-subscription-types)，则可以在现有订阅中设置托管实例。

**为什么无法在子网中设置名称以数字开头的托管实例？**

这是对基于 regex ^ [a zA-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (？ <！ \. 的基本组件的当前限制\s] ) $。 所有通过 regex 的名称和都是有效的子网名称。

**如何缩放托管实例？**

可以通过 [Azure 门户](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation)、 [PowerShell](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell)、 [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-update) 或 [ARM 模板](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates)缩放托管实例。

**是否可以将托管实例从一个区域移到另一个区域？**

可以。 有关说明，请参阅 [跨区域移动资源](../database/move-resources-across-regions.md)。

**如何删除托管实例？**

可以通过 Azure 门户、 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance?view=azps-4.3.0)、 [AZURE CLI](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-delete) 或 [资源管理器 REST api](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)删除托管实例。

**创建或更新实例或还原数据库需要多长时间？**

需要创建新的托管实例或更改服务层 (Vcore，存储) 的时间取决于多个因素。 请参阅 [管理操作](sql-managed-instance-paas-overview.md#management-operations)。
 
## <a name="naming-conventions"></a>命名约定

**托管实例是否可与 SQL Server本地实例同名？**

不支持更改托管实例名称。

**能否更改 DNS 区域前缀？**

是，托管实例默认 DNS 区域 *。 database.windows.net* 可以更改。 

若要使用其他 DNS 区域而不是默认区域（例如“.contoso.com”），请执行以下操作： 
- 使用 CliConfig 定义别名。 该工具只是一个注册表设置包装，因此也可以使用组策略或脚本完成此操作。
- 将 CNAME 与 TrustServerCertificate=true 选项一起使用 。

## <a name="migration-options"></a>迁移选项

**如何从 Azure SQL Database 单一或弹性池迁移到 SQL 托管实例？**

托管实例根据计算和存储大小提供与其他 Azure SQL 数据库部署选项相同的性能级别。 若要在单一实例上合并数据，或者只是需要一种仅在托管实例中受支持的功能，可以使用导出/导入 (BACPAC) 功能来迁移数据。 下面是用于 sql 数据库迁移到 SQL 托管实例的其他方法： 
- 使用 [外部数据源](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- 使用 [SQLPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- 使用 [BCP](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**如何将实例数据库迁移到单个 Azure SQL 数据库？**

一种选择是将 [数据库导出到 bacpac](../database/database-export.md) ，然后 [导入 bacpac 文件](../database/database-import.md)。 如果数据库小于 100 GB，则建议使用此方法。

如果数据库中的所有表都具有*主键*，并且数据库中没有内存中 OLTP 对象，则可以使用[事务复制](replication-two-instances-and-sql-server-configure-tutorial.md?view=sql-server-2017)。

由于托管实例的数据库版本比 SQL Server 更高，因此无法将来自托管实例的本机 COPY_ONLY 备份还原到 SQL Server 中。 有关更多详细信息，请参阅 [仅复制备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server?view=sql-server-ver15)。

**如何将 SQL Server 实例迁移到 SQL 托管实例？**

若要迁移 SQL Server 实例，请参阅 [SQL Server 实例迁移到 AZURE SQL 托管实例](migrate-to-instance-from-sql-server.md)。

**如何从其他平台迁移到 SQL 托管实例？**

有关从其他平台迁移的迁移信息，请参阅 [Azure 数据库迁移指南](https://datamigration.microsoft.com/)。

## <a name="switch-hardware-generation"></a>切换硬件代系 

**能否在第 4 代和第 5 代托管实例硬件代系之间联机切换？**

如果在预配托管实例的区域中提供 Gen5 硬件，则可以从 Gen4 到 Gen5 的自动联机切换。 在这种情况下，可以查看[“vCore 模型概述”页](../database/service-tiers-vcore.md)，该页说明了如何在硬件代系之间切换。

这是一个长时间运行的操作，因为新托管实例将在后台预配，数据库将在旧实例与新实例之间自动转移，该过程结束时，可以快速故障转移。

注意： Gen4 硬件正在逐步推出，在新部署中不再可用。 所有新数据库都必须部署在 Gen5 硬件上。 从 Gen5 切换到 Gen4 也不可用。

## <a name="performance"></a>性能 

**如何将托管实例性能进行比较以 SQL Server 性能？**

若要在托管实例与 SQL Server 之间进行性能比较，可以从[有关 Azure SQL 托管实例与 SQL Server 之间的性能比较的最佳做法](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210)一文入手。

**托管实例和 SQL Server 会导致性能差异？**

请参阅 [SQL 托管实例和 SQL Server 之间性能差异的主要原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)。 有关日志文件大小对常规用途托管实例性能的影响的详细信息，请参阅 [常规用途上的日志文件大小的影响](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)。

**如何优化托管实例的性能？**

可以通过以下方式优化托管实例的性能：
- [自动优化](../database/automatic-tuning-overview.md) ，通过基于 AI 和机器学习的持续性能优化，提供高峰性能和稳定工作负荷。
-   [内存中 OLTP](../in-memory-oltp-overview.md) 可提高事务处理工作负荷的吞吐量和延迟，并提供更快的商业洞察力。 

若要进一步优化性能，请考虑为[应用程序和数据库优化](../database/performance-guidance.md#tune-your-database)应用一些*最佳实践*。
如果工作负荷包含大量小事务，请考虑将 [连接类型从代理切换到重定向模式](connection-types-overview.md#changing-connection-type) ，以降低延迟和提高吞吐量。

## <a name="monitoring-metrics-and-alerts"></a>监视、指标和警报

**对于托管实例，监视和警报有哪些选项？**

有关监视和警报 SQL 托管实例使用情况和性能的所有可能的选项，请参阅 [AZURE sql 托管实例监视选项博客文章](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416)。 有关 SQL MI 的实时性能监视，请参阅 [AZURE SQL DB 的实时性能监视托管实例](https://docs.microsoft.com/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)。

**是否可以使用 SQL 事件探查器进行性能跟踪？**

是的，支持 SQL 事件探查器或 SQL 托管实例。 有关更多详细信息，请参阅 [SQL 事件探查器](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler?view=sql-server-ver15)。

**托管实例数据库是否支持数据库顾问和 Query Performance Insight？**

不支持，它们不受支持。 可以结合使用 [dmv](../database/monitoring-with-dmvs.md) 和 [查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-ver15) 与 [SQL Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler?view=sql-server-ver15) 和 [XEvents](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events?view=sql-server-ver15) 来监视数据库。

**能否在 SQL 托管实例上创建指标警报？**

是的。 有关说明，请参阅为 [SQL 托管实例创建警报](alerts-create.md)。

**能否在托管实例中的数据库上创建指标警报？**

不能，警报指标仅适用于托管实例。 警报指标不适用于托管实例中的单个数据库。

## <a name="storage-size"></a>存储大小

**SQL 托管实例的最大存储大小是多少？**

SQL 托管实例的存储大小取决于所选的服务层级（“常规用途”或“业务关键”）。 有关这些服务层级的存储限制，请参阅[服务层级特征](../database/service-tiers-general-purpose-business-critical.md)。

**可用于托管实例的最小存储大小是多少？**

实例中可用的最小存储量为 32 GB。 可以将存储添加为 32 GB 的增量，最大限度地提高存储大小。 第一32GB 是免费的。

**是否可以增加分配给实例的存储空间，而不受计算资源的影响？**

是的，你可以独立于计算将附加存储购买到一定范围。 请参阅[表](resource-limits.md#hardware-generation-characteristics)中的*最大实例保留存储*。

**如何在常规用途服务层中优化存储性能？**

若要优化存储性能，请参阅 [常规用途中的存储最佳实践](https://techcommunity.microsoft.com)。

## <a name="backup-and-restore"></a>备份和还原

**备份存储是否是从托管实例存储中扣减出来的？**

不是，备份存储不是从托管实例的存储空间中扣减出来的。 备份存储与实例存储空间无关，其大小不受限制。 备份存储限制为保留实例数据库备份的时间段，可配置最多35天。 有关详细信息，请参阅[自动化备份](../database/automated-backups-overview.md)。

**如何查看托管实例上的自动备份？**

若要跟踪何时对托管实例执行了自动备份，请参阅 [如何跟踪 AZURE SQL 托管实例的自动备份](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355)。

**是否支持按需备份？**

是的，你可以在其 Azure Blob 存储中创建仅复制完整备份，但它只能在托管实例中进行还原。 有关详细信息，请参阅 [仅复制备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server?view=sql-server-ver15)。 但是，如果数据库由服务托管的 TDE 加密，则不可能进行仅复制备份，因为用于加密的证书不可访问。 在这种情况下，请使用时间点还原功能将数据库移到另一个 SQL 托管实例或切换到客户托管的密钥。

** (从 .bak 文件) 到托管实例受支持的本机还原吗？**

是的，它受支持，可用于 SQL Server 2005 以上版本。  若要使用本机还原，请将 .bak 文件上传到 Azure blob 存储并执行 T-sql 命令。 有关更多详细信息，请参阅 [从 URL 本机还原](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate#native-restore-from-url)。

## <a name="business-continuity"></a>业务连续性

**系统数据库是否已复制到故障转移组中的辅助实例？**

系统数据库不会复制到故障转移组中的辅助实例。 因此，除非在辅助实例上手动创建对象，否则依赖于系统数据库中的对象的方案将不可能在辅助实例上出现。 有关解决方法，请参阅 [从系统数据库启用依赖于对象的方案](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases)。
 
## <a name="networking-requirements"></a>网络要求 

**托管实例子网上的当前入站/出站 NSG 约束是什么？**

所需的 NSG 和 UDR 规则记录在 [此处](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)，并由服务自动设置。
请记住，这些规则只是维护服务所需的规则。 若要连接到托管实例并使用不同的功能，您将需要设置需要维护的附加功能特定规则。

**如何针对管理端口设置入站 NSG 规则？**

SQL 托管实例负责设置管理端口的规则。 这是通过名为 [服务辅助子网配置](connectivity-architecture-overview.md#service-aided-subnet-configuration)的功能来实现的。
这是为了确保不中断管理流量的流动，以便满足 SLA。

**能否获取用于入站管理流量的源 IP 范围？**

是的。 可以通过 [配置网络观察程序流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group)来分析通过网络安全组传入的流量。

**是否可以将 NSG 设置为控制对 (端口 1433) 的数据终结点的访问？**

是的。 设置托管实例后，可以设置控制对端口1433的入站访问的 NSG。 建议尽可能缩小其 IP 范围。

**是否可以设置 NVA 或本地防火墙以根据 Fqdn 筛选出站管理流量？**

不是。 由于以下几个原因，不支持此操作：
-   表示对入站管理请求的响应的路由流量将是非对称的，因此无法运行。
-   转到存储的路由流量将受到吞吐量限制和延迟的影响，因此，我们无法提供预期的服务质量和可用性。
-   根据经验，这些配置容易出错并且不受支持。

**能否为出站非管理流量设置 NVA 或防火墙？**

是的。 实现此目的的最简单方法是将0/0 规则添加到与托管实例子网关联的 UDR，以通过 NVA 路由流量。
 
**托管实例需要多少 IP 地址？**

子网必须有足够数量的可用 [IP 地址](connectivity-architecture-overview.md#network-requirements)。 若要确定 SQL 托管实例的 VNet 子网大小，请参阅 [确定托管实例所需的子网大小和范围](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet)。 

**如果没有足够的 IP 地址来执行实例更新操作，该怎么办？**

如果在预配托管实例的子网中没有足够的 [IP 地址](connectivity-architecture-overview.md#network-requirements) ，则必须在其中创建新的子网和新的托管实例。 同时建议在创建新的子网时分配更多 IP 地址，以免在将来的更新操作中遇到类似情况。 预配新实例后，可以在新实例和新实例之间手动备份和还原数据，或执行跨实例 [时间点还原](point-in-time-restore.md?tabs=azure-powershell)。

**是否需要空的子网来创建托管实例？**

不是。 你可以使用已包含托管实例 (s) 的空子网或子网。 

**能否更改子网地址范围？**

如果中有托管实例，则不能。 这是一种 Azure 网络基础结构的限制。 只允许 [将其他地址空间添加到空白子网](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings)。 

**是否可以将托管实例移到另一个子网？**

不是。 这是当前托管实例的设计限制。 但是，你可以在另一个子网中预配新的实例，并在旧实例与新实例之间手动备份和还原数据，或执行跨实例 [时间点还原](point-in-time-restore.md?tabs=azure-powershell)。

**是否需要空虚拟网络来创建托管实例？**

这不是必需的。 可以 [为 AZURE sql 托管实例创建虚拟网络](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet) ，也可以 [为 Azure sql 托管实例配置现有虚拟网络](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet)。

**是否可以将托管实例与其他服务一起放入子网？**

不是。 目前，我们不支持将托管实例放置在已包含其他资源类型的子网中。

## <a name="connectivity"></a>连接 

**是否可以使用 IP 地址连接到托管实例？**

否，不支持这样做。 托管实例的主机名映射到托管实例虚拟群集前面的负载均衡器。 由于一个虚拟群集可以托管多个托管实例，因此无法将连接路由到正确的托管实例，无需指定其名称。
有关 SQL 托管实例虚拟群集体系结构的详细信息，请参阅[虚拟群集连接体系结构](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)。

**我的托管实例是否可以使用静态 IP 地址？**

目前不支持。

在罕见但必要的情况下，我们可能需要将托管实例联机迁移到新的虚拟群集。 需要进行这种迁移的原因是，我们的技术堆栈发生了变化，旨在提高服务的安全性和可靠性。 迁移到新的虚拟群集会导致映射到托管实例主机名的 IP 地址发生变化。 托管实例服务不会提出静态 IP 地址支持，且有权在不另行通知的情况下，在定期维护周期更改此 IP 地址。

出于此原因，我们强烈反对依赖于 IP 地址的不可变性，因为这可能会导致不必要的停机时间。

**托管实例是否有公用终结点？**

是的。 托管实例具有一个公共终结点，该终结点默认仅用于服务管理，但客户也可以启用它来访问数据。 有关更多详细信息，请参阅 [将 SQL 托管实例与公共终结点一起使用](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-securely)。 若要配置公共终结点，请参阅 [在 SQL 托管实例中配置公共终结点](public-endpoint-configure.md)。

**托管实例如何控制对公共终结点的访问？**

托管实例控制在网络和应用程序级别对公共终结点的访问。

管理和部署服务使用映射到外部负载均衡器的[管理终结点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#management-endpoint)连接到托管实例。 仅当流量是在一组专用于托管实例管理组件的预定义端口上收到的时，才将流量路由到节点。 节点上的内置防火墙设置为只允许来自 Microsoft IP 范围的流量。 证书将对管理组件与管理平面之间的所有通信进行相互身份验证。 有关更多详细信息，请参阅 [SQL 托管实例的连接体系结构](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#virtual-cluster-connectivity-architecture)。

**是否可以使用公共终结点来访问托管实例数据库中的数据？**

是的。 客户需要从[Azure 门户](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)PowerShell/ARM 启用公共终结点数据访问  /  [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) ，并将 NSG 配置为锁定对 (端口号 3342) 的数据端口的访问。 有关详细信息，请参阅 [在 AZURE sql 托管实例中配置公共终结点](public-endpoint-configure.md) 和通过 [公共终结点安全使用 Azure SQL 托管实例](public-endpoint-overview.md)。 

**能否为 SQL 数据终结点 (s) 指定自定义端口？**

否，此选项不可用。  对于专用数据终结点，托管实例使用默认端口号1433，对于公共数据终结点，托管实例使用默认端口号3342。

**将托管实例连接到不同区域的建议方法是什么？**

快速路由线路对等互连是执行此操作的首选方式。 这不会与跨区域虚拟网络对等互连，因为与内部负载均衡器相关的 [约束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)不受支持。

如果无法实现快速路由线路对等互连，则唯一的另一种方法是创建站点到站点 VPN 连接 ([Azure 门户](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)， [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)， [Azure CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)) 。

## <a name="mitigate-data-exfiltration-risks"></a>缓解数据透露风险  

**如何缓解数据透露风险？**

为了缓解任何数据透露风险，我们建议客户应用一组安全设置和控制：

- 在所有数据库上启用[透明数据加密 (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql)。
- 关闭公共语言运行时 (CLR)。 也建议在本地禁用 CLR。
- 仅使用 Azure Active Directory (Azure AD) 身份验证。
- 使用低特权 DBA 帐户访问实例。
- 为 sysadmin 帐户配置 JiT jumpbox 访问权限。
- 启用 [SQL 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)，并将其与警报机制相集成。
- 启用[高级数据安全 (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 套件中的[威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)。

## <a name="dns"></a>DNS

**能否为 SQL 托管实例配置自定义 DNS？**

是的。 请参阅 [如何为 AZURE SQL 托管实例配置自定义 DNS](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。

**是否可以执行 DNS 刷新？**

目前我们不提供刷新 SQL 托管实例 DNS 服务器配置的功能。

DNS 配置最终会刷新：

- 当 DHCP 租约过期时。
- 平台升级时。

一种解决方法是将 SQL 托管实例降级为 4 个 vCore，然后再将其升级。 这样刷新 DNS 配置会产生一种负面影响。

## <a name="change-time-zone"></a>更改时区

**是否可以更改现有托管实例的时区？**

首次预配托管实例时可以设置时区配置。 不支持更改现有托管实例的时区。 有关详细信息，请参阅[时区限制](timezones-overview.md#limitations)。

解决方法包括使用适当的时区创建新的托管实例，然后执行手动备份和还原，我们建议执行[跨实例时间点还原](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)。


## <a name="security-and-database-encryption"></a>安全性和数据库加密

**Sysadmin 服务器角色是否可用于 SQL 托管实例？**

是，客户可以创建作为 sysadmin 角色成员的登录名。  假定 sysadmin 权限的客户也被认为是操作实例的责任，这会对 SLA 承诺产生负面影响。 若要将登录名添加到 sysadmin 服务器角色，请参阅 [Azure AD authentication](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-aad-security-tutorial#azure-ad-authentication)。

**SQL 托管实例是否支持透明数据加密？**

是的，SQL 托管实例支持透明数据加密。 有关详细信息，请参阅 [SQL 托管实例透明数据加密](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal)。

**能否对 TDE 使用 "自带密钥" 模型？**

是的，适用于 BYOK 方案 Azure Key Vault 适用于 Azure SQL 托管实例。 有关详细信息，请参阅 [与客户托管的密钥透明数据加密](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-ver15&tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)。

**是否可以迁移加密的 SQL Server 数据库？**

可以。 要迁移加密的 SQL Server 数据库，需要将现有证书导出并导入到托管实例中，然后执行完整数据库备份并将其还原到托管实例中。 

你还可以使用 [Azure 数据库迁移服务](https://azure.microsoft.com/services/database-migration/) 迁移 TDE 加密数据库。

**如何为 SQL 托管实例配置 TDE 保护程序轮换？**

您可以使用 Azure Cloud Shell 来旋转托管实例的 TDE 保护程序。 有关说明，请参阅 [SQL 托管实例中的透明数据加密 Azure Key Vault 使用你自己的密钥](scripts/transparent-data-encryption-byok-powershell.md)。

**是否可将加密的数据库还原到 SQL 托管实例？**

可以，无需解密数据库即可将其还原到 SQL 托管实例。 需将一个在源系统中用作加密密钥保护器的证书/密钥提供给 SQL 托管实例，才能从加密的备份文件中读取数据。 要运行此操作有两个可行的方式：

- 将证书保护器上传到 SQL 托管实例。 只能使用 PowerShell 执行此操作。 [示例脚本](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate)描述了整个过程。
- 将非对称密钥保护器上传到 Azure Key Vault，并将 SQL 托管实例指向该保护器。 此方法类似于自带密钥 (BYOK) TDE 用例，该用例也使用 Key Vault 集成来存储加密密钥。 如果你不想将该密钥用作加密密钥保护器，只想为 SQL 托管实例提供密钥来还原加密的数据库，请按照有关[设置 BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption) 的说明进行操作，不要选中“将所选密钥设为默认 TDE 保护器”复选框。

将加密保护器提供给 SQL 托管实例使用后，可以继续执行标准的数据库还原过程。

## <a name="purchasing-models-and-benefits"></a>购买模型和权益

**SQL 托管实例有哪些购买模型？**

SQL 托管实例提供 [基于 vCore 的购买模型](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)。

**SQL 托管实例有哪些成本优势？**

可以通过以下方式节省 Azure SQL 权益的成本：
-   最大限度地提高本地许可证的现有投资，并通过 [Azure 混合权益](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell)节省高达55%。 
-   提交给计算资源的预订，并使用 [保留实例权益](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity)节省高达33%。 结合 Azure 混合权益，节省高达82%。 
-   利用 [Azure 开发/测试定价权益](https://azure.microsoft.com/pricing/dev-test/) ，为正在进行的开发和测试工作负荷提供折扣率，可节省高达55% 与标价。

**谁有资格获得预订实例权益？**

若要获得预订实例权益的资格，订阅类型必须是企业协议 (产品/服务（BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p) ）或使用即用即付定价 (产品/服务的个人协议产品/服务) 。 有关预订的详细信息，请参阅 [保留实例权益](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity)。 

**是否可以取消、交换或退款预留？**

您可以取消、交换或退款保留，但有一些限制。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)。

## <a name="billing-for-managed-instance-and-backup-storage"></a>托管实例和备份存储计费

**什么是 SQL 托管实例定价选项？**

若要浏览托管实例定价选项，请参阅 [定价页](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)。

**如何跟踪托管实例的计费成本？**

可以使用 [Azure 成本管理解决方案](https://docs.microsoft.com/azure/cost-management-billing/)实现此目的。 导航到[Azure 门户](https://portal.azure.com)中的 "**订阅**"，然后选择 "**成本分析**"。 

使用 **累积成本** 选项，并按 **资源类型** 筛选 `microsoft.sql/managedinstances` 。

**自动备份的成本是多少？**

不管备份保持期设置如何，都可以获得与购买的保留数据存储空间相同的可用备份存储空间量。 如果备份存储消耗在已分配的可用备份存储空间内，则托管实例上的自动备份将不会产生额外的费用，因此将不收取任何费用。 超过可用空间的情况下，如果超过可用空间，将会产生大约 $0.20-$0.24 美元/月的费用，或者查看定价页以了解你所在区域的详细信息。 有关更多详细信息，请参阅 [备份存储消耗说明](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)。

**如何监视备份存储消耗的计费成本？**

可以通过 Azure 门户监视备份存储的成本。 有关说明，请参阅 [监视自动备份的成本](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs)。 

**如何优化托管实例上的备份存储成本？**

若要优化备份存储成本，请参阅 [SQL 托管实例上的精细备份优化](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935)。

## <a name="cost-saving-use-cases"></a>有关节省成本的用例

**在何处可以找到用例，以及通过 SQL 托管实例可实现的成本节省？**

SQL 托管实例案例研究：

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

为了让用户更好地了解部署 Azure SQL 托管实例的优势、成本和风险，我们还提供了一份 Forrester 案例研究：[Microsoft Azure SQL 数据库托管实例的总体经济影响](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。

## <a name="password-policy"></a>密码策略 

**对于 SQL 托管实例 SQL 登录名，可应用什么密码策略？**

用于 SQL 登录名的 SQL 托管实例密码策略沿袭了Azure 平台策略，该策略应用于构成保存托管实例的虚拟群集的 VM。 目前不能更改这些设置，因为这些设置由 Azure 定义并由托管实例继承。

 > [!IMPORTANT]
 > Azure 平台可能会更改策略要求，且不会通知依赖于该策略的服务。

**当前的 Azure 平台策略是什么？**

每个登陆者必须在登录时设置其密码，并在达到最长密码期限后更改其密码。

| **策略** | **安全设置** |
| --- | --- |
| 最长密码期限 | 42 天 |
| 最短密码期限 | 1 天 |
| 最短密码长度 | 10 个字符 |
| 密码必须符合复杂性要求 | Enabled |

**是否可以在登录级别禁用 SQL 托管实例中的密码复杂性和过期时间？**

是的，可以在登录级别控制 CHECK_POLICY 和 CHECK_EXPIRATION 字段。 可以执行以下 T-SQL 命令来检查当前设置：

```sql
SELECT *
FROM sys.sql_logins
```

之后，可以执行以下操作来修改指定的登录设置：

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

（将“test”替换为所需的登录 ID，并调整策略和过期时间值）


## <a name="service-updates"></a>服务更新

**什么是 SQL 托管实例的计划内维护事件？**

请参阅 [规划 SQL 托管实例中的 Azure 维护事件](https://docs.microsoft.com/azure/azure-sql/database/planned-maintenance)。 


## <a name="azure-feedback-and-support"></a>Azure 反馈和支持

**在哪里可以保留我对 SQL 托管实例改进的看法？**

你可以为新的托管实例功能投票，或对 [SQL 托管实例反馈论坛](https://feedback.azure.com/forums/915676-sql-managed-instance)上的投票提供新的改进建议。 这样一来，您就可以对产品开发做出贡献，并帮助我们确定潜在改进的优先级。

**如何创建 Azure 支持请求？**

若要了解如何创建 Azure 支持请求，请参阅 [如何创建 azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。


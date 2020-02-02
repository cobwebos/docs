---
title: 选择适当的部署类型-Azure Database for MySQL
description: 本文介绍将 Azure Database for MySQL 部署为基础结构即服务（IaaS）或平台即服务（PaaS）之前应考虑的因素。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 8cff61d547e75b186869b3ab4d57c5eb12e6e2f5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935469"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>在 Azure 中选择正确的 MySQL Server 选项

使用 Azure，MySQL server 工作负载可以在托管的虚拟机基础结构即服务（IaaS）或作为托管的平台即服务（PaaS）中运行。 PaaS 具有多个部署选项，并且每个部署选项中都有服务层。 当你在 IaaS 和 PaaS 之间进行选择时，必须决定是要管理你的数据库、应用修补程序并进行备份，还是要将这些操作委派给 Azure。

做出决定时，请考虑以下两个选项：

- **Azure Database for MySQL**。 此选项是一个完全托管的 MySQL 数据库引擎，基于 MySQL 社区版的稳定版本。 此关系数据库即服务（DBaaS）在 Azure 云平台上托管，属于 PaaS 的行业类别。

  使用 Azure 上的托管 MySQL 实例，你可以使用在 MySQL Server 位于本地或 Azure VM 时需要进行广泛配置的内置功能。

  使用 MySQL 作为服务时，你可以根据需要进行扩展或横向扩展以实现更好的控制，无需中断。 与独立的 MySQL Server 不同，Azure Database for MySQL 提供内置的高可用性、智能和管理等其他功能。

- **Azure vm 上的 MySQL**。 此选项属于 IaaS 的行业类别。 使用此服务，你可以在 Azure 云平台上的完全托管的虚拟机中运行 MySQL Server。 所有最新版本的 MySQL 都可以安装在 IaaS 虚拟机上。

  与 Azure Database for MySQL 的不同之处在于，Azure Vm 上的 MySQL 提供对数据库引擎的控制。 但是，此控制将承担管理 Vm 和许多数据库管理（DBA）任务所需的责任。 这些任务包括维护和修补数据库服务器、数据库恢复和高可用性设计。

下表列出了这些选项之间的主要差别：

|            | Azure Database for MySQL | Azure Vm 上的 MySQL    |
|:-------------------|:-----------------------------|:--------------------|
| 服务级别协议 (SLA)                | 提供99.99% 可用性的 SLA| 同一可用性集中的两个或更多实例的可用性最高可达99.95%。<br/><br/>使用高级存储的单个实例 VM 的可用性为99.9%。<br/><br/>使用可用性区域在多个可用性集中具有多个实例的99.99%。<br/><br/>请参阅[虚拟机 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)。 |
| 操作系统修补        | 自动  | 由客户管理 |
| MySQL 修补     | 自动  | 由客户管理 |
| 高可用性 | 高可用性（HA）模型基于内置故障转移机制，适用于发生节点级中断的时间。 在这种情况下，服务将自动创建一个新实例，并将存储附加到此实例。 | 客户构建、测试和维护高可用性。 功能可能包括群集、复制等。|
| 区域冗余 | 目前不受支持。 | 可以将 Azure Vm 设置为在不同的可用性区域中运行。 对于本地解决方案，客户必须创建、管理和维护其自己的辅助数据中心。|
| 混合场景 | 使用[数据传入复制](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)，你可以将外部 MySQL 服务器中的数据同步到 Azure Database for MySQL 服务。 外部服务器可以处于本地、虚拟机中或是其他云提供商托管的数据库服务。<br/><br/> 使用[读取副本](https://docs.microsoft.com/azure/mysql/concepts-read-replicas)功能，可以将 Azure Database for MySQL 主服务器中的数据复制到最多5个只读副本服务器。 副本在同一 Azure 区域内或跨区域。 使用 binlog 复制技术异步更新只读副本。| 由客户管理
| 备份和还原 | 自动创建[服务器备份](https://docs.microsoft.com/azure/mysql/concepts-backup#backups)并将其存储在用户配置的存储中，该存储是本地冗余或异地冗余的。 该服务将进行完整备份、差异备份和事务日志备份 | 由客户管理 |
| 监视数据库操作 | 为客户提供对数据库操作[设置警报](https://docs.microsoft.com/azure/mysql/concepts-monitoring)的功能，并在达到阈值时采取措施。 | 由客户管理 |
| 高级威胁防护 | 提供[高级威胁防护](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal)。 此保护可检测异常活动，这些活动表示异常和可能有害的数据库访问或利用尝试。 | 客户必须为自己构建这种保护。
| 灾难恢复 | 将自动备份存储在用户配置的[本地冗余或异地冗余存储](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)中。 备份还可以将服务器还原到某个时间点。 保持期为7到35天。 还原是通过使用 Azure 门户来完成的。 | 完全由客户管理。 责任包括但不限于计划、测试、存档、存储和保留。 另一种方法是使用 Azure 恢复服务保管库备份 Vm 上的 Azure Vm 和数据库。 此选项处于预览阶段。 |
| 性能建议 | 为客户提供基于系统生成的使用情况日志文件的[性能建议](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110)。 建议有助于优化工作负荷。 | 由客户管理 |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>选择 PaaS 或 IaaS 的业务动机

有多种因素可能会影响选择 PaaS 或 IaaS 来托管 MySQL 数据库的决策。

### <a name="cost"></a>费用

有限的资金通常是确定用于托管数据库的最佳解决方案的主要考虑因素。 无论你是在一家已建立的公司中使用极少的现金或团队，还是在超出预算限制的情况下启动的，都是如此。 本部分介绍 Azure 中的计费和许可基础知识，因为它们适用于 Azure Vm 上的 Azure Database for MySQL 和 MySQL。

#### <a name="billing"></a>计费

Azure Database for MySQL 当前在具有不同资源价格的多个层中作为服务提供。 所有资源都按固定费率按小时计费。 有关当前支持的服务层、计算大小和存储量的最新信息，请参阅[基于 vCore 的购买模型](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)。 你可以根据应用程序的不同吞吐量需求，动态调整服务层和计算大小。 按常规[数据传输费率](https://azure.microsoft.com/pricing/details/data-transfers/)对传出 Internet 流量收费。

在 Azure Database for MySQL 中，Microsoft 自动配置、修补和升级数据库软件。 这些自动操作降低了管理成本。 此外，Azure Database for MySQL 还[提供内置的备份](https://docs.microsoft.com/azure/mysql/concepts-backup)功能。 这些功能可帮助你显著节省成本，尤其是在拥有大量数据库的情况下。 与在 Azure Vm 上运行 MySQL 相比，你可以选择并运行任何 MySQL 版本。 无论使用何种 MySQL 版本，都需要为预配的 VM 付费，并为使用的特定 MySQL 许可证类型收费。

Azure Database for MySQL 为任何类型的节点级别中断提供内置的高可用性，同时仍然维护服务的 99.99% SLA 保证。 但是，对于 Vm 内的数据库高可用性，客户应使用可用于 MySQL 数据库的高可用性选项，如[mysql 复制](https://dev.mysql.com/doc/refman/8.0/en/replication.html)。 使用支持的高可用性选项不提供额外的 SLA。 但它确实允许以额外的成本和管理开销来实现超过99.99% 的数据库可用性。

有关定价的详细信息，请参阅以下文章：
* [Azure Database for MySQL 定价](https://azure.microsoft.com/pricing/details/mysql/)
* [虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>管理

对于许多企业来说，迁移到云服务的决定都非常多地减轻管理复杂性。 对于 IaaS 和 PaaS，Microsoft：

- 管理底层基础结构。
- 自动复制所有数据以提供灾难恢复。
- 配置和升级数据库软件。
- 管理负载平衡。
- 如果出现服务器故障，则执行透明故障转移。

以下列表描述了每个选项的管理注意事项：

* 利用 Azure Database for MySQL，你可以继续管理数据库。 但不再需要管理数据库引擎、操作系统或硬件。 可以继续管理的项目示例包括：

  - 数据库
  - 登录
  - 索引优化
  - 查询优化
  - 审核
  - 安全性

  此外，将高可用性配置为另一数据中心需要进行少量的配置或管理。

* 使用 Azure Vm 上的 MySQL，可以完全控制操作系统和 MySQL 服务器实例配置。 使用 VM，可以决定何时更新或升级操作系统与数据库软件。 你还决定何时安装任何其他软件，如防病毒应用程序。 提供了一些自动化功能，可大大简化修补、备份和高可用性。 可以控制 VM 的大小、磁盘数目及其存储配置。 有关详细信息，请参阅[Azure 的虚拟机和云服务大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。

### <a name="time-to-move-to-azure"></a>转到 Azure 的时间

* 当开发人员的工作效率和新解决方案的面市时间非常重要时，Azure Database for MySQL 是适用于云设计的应用程序的正确解决方案。 对于像 DBA 这样的编程功能，此服务适用于云架构师和开发人员，因为它降低了管理基础操作系统和数据库的需求。

* 如果希望避免获取新的本地硬件所需的时间和费用，Azure Vm 上的 MySQL 是适用于需要 MySQL 数据库的应用程序或 Windows 或 Linux 上的 MySQL 功能的合适解决方案。 此解决方案还适用于将现有本地应用程序和数据库原样迁移到 Azure，适用于 Azure Database for MySQL 不太合适的情况。

  由于无需更改呈现、应用程序和数据层，因此可以在重新架构现有解决方案时节省时间和预算。 相反，你可以将重点放在将所有解决方案迁移到 Azure，并解决 Azure 平台可能需要的某些性能优化。

## <a name="next-steps"></a>后续步骤

* 请参阅[Azure Database for MySQL 定价](https://azure.microsoft.com/pricing/details/MySQL/)。
* 从[创建第一个服务器](https://review.docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal)开始。

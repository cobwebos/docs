---
title: 为 Azure Database for MariaDB 选择正确的部署类型
description: 本文介绍了在针对 Azure Database for MariaDB 的基础结构即服务（IaaS）或平台即服务（PaaS）进行操作之前需要完成的注意事项。
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: fa87748719e2d3775034e5a2850281cf8f1a0e8a
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817431"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>在 Azure 中选择正确的 MariaDB 服务器选项

使用 Azure，MariaDB 服务器工作负荷可以在托管的基础结构 VM （IaaS）中运行，也可以作为托管服务运行（PaaS）。 PaaS 具有多个部署选项，并且每个部署选项中都有服务层。 在 PaaS 或 IaaS 之间进行决定时，需要确定是要管理数据库、应用修补程序并进行备份，还是要将这些操作委派给 Azure。</br>

根据此问题的答案，请考虑以下选项： <br/>

**Azure Database for MariaDB**是一种完全托管的 MariaDB 数据库引擎，它基于社区版的稳定版本。 这种关系数据库即服务（DBaaS）在 Azure 云中托管，属于平台即服务（PaaS）的行业类别。 使用 Azure 上的 MariaDB 托管实例，可以在使用 MariaDB 服务器（本地或 Azure 虚拟机）时，使用内置特性和需要广泛配置的功能。 使用 MariaDB 作为服务时，即用即付选项，可以增加或减少以实现更好的电源，无需中断。 此外，与独立的 MariaDB 服务器不同的是，Azure Database for MariaDB 提供内置的高可用性、智能和管理等其他功能。 <br/><br/>
**AZURE VM 上的 MariaDB**属于行业类别的基础结构即服务（IaaS），可让你在 Azure 云中完全托管的虚拟机中运行 MariaDB 服务器。 所有最新版本的 MariaDB 都可以安装在 IaaS 虚拟机上。 与 Azure Database for MariaDB 的最大差别在于，Azure Vm 上的 MariaDB 允许对数据库引擎进行控制。 但是，此控制会带来额外责任来管理虚拟机和大量 DBA 任务，如数据库服务器维护/修补、恢复和高可用性设计等。

下表列出了这些选项之间的主要差别：

|            | **Azure Database for MariaDB** | **Azure Vm 上的 MariaDB**    |
|:-------------------|:-----------------------------|:--------------------|
| **SLA**                | 提供 99.99% 可用性的 SLA| 在同一可用性集中有2个或更多实例的可用性最高可达 99.95%。 <br/>99.9% 单实例 VM 使用高级存储 <br/> 2个或更多可用性集中有2个或更多实例的可用性区域为 99.99%。<br/> 备注-[虚拟机 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **OS 修补**        | 自动  | 由客户管理 |
|**MariaDB 修补**     | 自动  | 由客户管理 |
| **高可用性** | 高可用性 (HA) 模型以节点级中断发生时的内置故障转移机制为依据。 在这种情况下，服务会自动创建实例，并将存储附加到此新实例。 | 高可用性由客户进行构建、实现、测试和维护。 这可能包括 Always On （故障转移群集或组复制）、日志传送和事务复制，这取决于所使用的 MariaDB engine 版本。|
| **区域冗余** | 当前不支持。 | 可以将 Azure Vm 设置为在不同的可用性区域中运行。 对于本地解决方案，客户应创建、管理和维护其自己的辅助数据中心。|
| **混合方案** | [数据传入复制](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication)允许你将数据从外部 MariaDB 服务器同步到 Azure Database for MariaDB 服务。 外部服务器可以处于本地、虚拟机中或是其他云提供商托管的数据库服务。  <br/> <br/> 使用 "[读取副本](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas)" 功能，可以将数据从 Azure Database for MariaDB 服务器（master）复制到同一 Azure 区域或多个区域中的最多五个只读服务器（副本）。 使用 binlog 复制技术异步更新只读副本。   <br/> <br/> 注意-跨区域读取复制当前为公共预览版。| 由客户管理 <br/>
| **备份和还原** | 自动创建[服务器备份](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups)并将其存储在用户配置的本地冗余或异地冗余存储中。 该服务将进行完整备份、差异备份和事务日志备份 | 由客户管理 |
| **监视数据库操作** | 允许客户设置有关数据库操作的[警报](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring)，并在达到阈值时采取措施。 | 由客户管理 |
| **高级线程保护** | 提供[高级威胁防护](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal)，用于检测异常活动，这些活动表明访问或利用数据库的异常和潜在有害尝试。 <br/> <br/> 注意-高级线程保护目前为公共预览版。| 客户必须自行构建。
| **备份（灾难恢复）** | 将自动备份存储在用户配置的[本地冗余或异地冗余存储](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal)中。 还可以使用备份将服务器还原到某个时间点。 保持期可设置为7-35 天。 可以使用 Azure 门户来完成还原。 <br/> | 完全由客户管理，包括但不限于计划、测试、存档、存储和保留。 另一种方法是使用 Azure 恢复服务保管库来备份 Vm 上的 Azure Vm 和数据库（预览版）。 |
| **性能建议** | 为客户提供基于使用情况遥测的主动[性能建议](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110)，以帮助优化工作负荷。 <br/> <br/> 注意-性能建议当前为公共预览版。 | 由客户管理 |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>选择 PaaS 或 IaaS 的业务动机

有几个因素可能会影响选择 PaaS 或 IaaS 来托管 MariaDB 数据库的决策：

### <a name="cost"></a>开销

无论你是在建立的公司中使用紧张预算限制运行的现金还是团队的现金不足，在确定托管数据库的最佳解决方案时，通常需要考虑有限的资金。 本部分介绍 Azure 中与 Azure Vm 上的 Azure Database for MariaDB 和 MariaDB 相关的计费和许可基础知识：

#### <a name="billing"></a>账单

目前，Azure Database for MariaDB 在多个层中作为一项服务提供，资源价格不同，每个层按固定费率按小时计费。 有关当前支持的服务层、计算大小和存储量的最新信息，请参阅[基于 vCore 的购买模型](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers)。 可以动态调整服务层级和计算大小，以满足应用程序的不同吞吐量需求。 按常规[数据传输费率](https://azure.microsoft.com/pricing/details/data-transfers/)对传出 Internet 流量收费。

使用 Azure Database for MariaDB，可自动配置、修补和升级数据库软件，从而降低管理成本。 此外，它的 [内置备份](https://docs.microsoft.com/azure/MariaDB/concepts-backup) 功能可帮助你大幅降低成本，尤其是拥有大量的数据库时。 对于 Azure Vm 上的 MariaDB，可以选择并运行任何 MariaDB 版本。 无论使用何种 MariaDB 版本，都需要为预配的 VM 和用于 MariaDB 的特定许可证类型收费。

Azure Database for MariaDB 为任何类型的节点级别中断提供内置的高可用性，并仍保留服务的 99.99% SLA。 但对于 Vm 内的数据库高可用性（HA），客户应通过 MariaDB 数据库上提供的高可用性选项，例如[MariaDB Replication](https://mariadb.com/kb/en/library/setting-up-replication/)。 使用支持的高可用性选项不提供额外的 SLA，但允许以额外的成本和管理开销来实现 99.99% 的数据库可用性 >。

有关定价的详细信息，请参阅以下资源：
* [Azure Database for MariaDB 定价](https://azure.microsoft.com/pricing/details/MariaDB/)
* [虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 价格计算器](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>管理

对于许多企业来说，迁移到云服务的决定就是减轻管理复杂性。 使用 IaaS 和 PaaS，Microsoft 管理底层基础结构并自动复制所有数据以提供灾难恢复，配置和升级数据库软件，管理负载均衡，并在有服务器发生故障。

* **利用 Azure Database for MariaDB**，你可以继续管理数据库，但不再需要管理数据库引擎、操作系统或硬件。 可以继续管理的项目示例包括数据库和登录、索引和查询优化，以及审核和安全性。 此外，将高可用性配置为另一数据中心需要很少的配置或无需进行配置或管理。<br/><br/>
* **使用 Azure vm 上的 MariaDB**，可以完全控制操作系统和 MariaDB 服务器实例配置。 使用 VM，可以决定何时更新/升级操作系统与数据库软件，以及何时安装任何其他软件，如防病毒应用程序。 提供的一些自动化功能能够大大简化修补、备份以及高可用性。 此外，还可以控制 VM 的大小、磁盘数目及其存储配置。 有关信息，请参阅 Azure 的虚拟机和云服务大小。

### <a name="time-to-move-to-azure-br"></a>转到 Azure 的时间 <br/>
* 当开发人员的工作效率和快速上市的新解决方案至关重要时， **Azure Database for MariaDB**是适用于云设计的应用程序的正确解决方案。 对于编程方式类似于 DBA 的功能，该服务非常适合云架构师和开发人员，因为这样会降低管理基础操作系统和数据库的需求。<br/><br/>
* **Azure vm 上的 MariaDB**非常适合于需要 MariaDB 数据库的现有或新应用程序或对 Windows/Linux 上 MariaDB 数据库中的功能的访问权限，并且你希望避免获取新的本地硬件所需的时间和费用。 此选项也适用于将现有本地应用程序和数据库按原样迁移到 Azure，在这种情况下，Azure Database for MariaDB 实例不是合适的。 由于无需更改呈现、应用程序和数据层，因此可以节省重新构建现有解决方案所需的时间和预算。 相反，你可以将重点放在将所有解决方案迁移到 Azure 中，并解决 Azure 平台可能需要的某些性能优化。

## <a name="next-steps"></a>后续步骤

* 请参阅[Azure Database for MariaDB 定价](https://azure.microsoft.com/pricing/details/MariaDB/)
* 从[创建第一个服务器](https://review.docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal)开始。


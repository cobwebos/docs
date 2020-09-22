---
title: 在 Azure 中选择适当的 PostgreSQL 服务器选项
description: 提供有关为部署选择适当的 PostgreSQL 服务器选项的指南。
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: f81a44af4a90ccda1875c0a9f2bfaa8dc0b73441
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943505"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>在 Azure 中选择适当的 PostgreSQL 服务器选项

借助 Azure，PostgreSQL 服务器工作负载可以在托管的虚拟机基础结构即服务 (IaaS) 或托管的平台即服务 (PaaS) 中运行。 PaaS 具有多个部署选项，每个选项都有多个服务层级。 在 IaaS 和 PaaS 之间选择时，必须决定是要管理数据库、应用修补程序并进行备份，还是要将这些操作委托给 Azure。

在制定决策时，请考虑在 PaaS 中使用以下三个选项，或者在 Azure VM (IaaS) 上运行
- [Azure Database for PostgreSQL 单一服务器](./overview-single-server.md)
- [Azure Database for PostgreSQL 灵活服务器](./flexible-server/overview.md)
- [Azure Database for PostgreSQL 超大规模 (Citus)]()

“Azure VM 上的 PostgreSQL”选项属于 IaaS 的行业类别。 使用此服务，可以在 Azure 云平台上的完全托管虚拟机中运行 PostgreSQL 服务器。 PostgreSQL 的所有最新版本都可以安装在 IaaS 虚拟机上。 Azure VM 上的 PostgreSQL 与 Azure Database for PostgreSQL 最大的区别在于，前者提供了对数据库引擎的控制。 但是，获得这种控制的代价是需要自行管理 VM 和许多数据库管理 (DBA) 任务。 这些任务包括维护和修补数据库服务器、数据库恢复和高可用性设计。

下表列出了这些选项之间的主要差别：

| **Attribute** | Azure VM 上的 Postgres | PostgreSQL 作为 PaaS |
| ----- | ----- | ----- |
| <B> 可用性 SLA |- 99.99%（可用性集） <br> - 99.95%（单个 VM） | - 单一服务器 - 99.99% <br> - 灵活服务器 - 预览版中不提供 <br> - 超大规模 (Citus) - 99.95%（启用高可用性时）|
| <B> OS 和 PostgreSQL 修补 | - 由客户管理 | - 单一服务器 - 自动执行 <br> - 灵活服务器 - 自动执行，具有可选的客户托管时段 <br> - 超大规模 (Citus) - 自动执行 |
| <B> 高可用性 | - 客户构建、实现、测试和维护高可用性。 功能可能包括聚类分析、复制等。 | - 单一服务器：内置 <br> - 灵活服务器：内置 <br> - 超大规模 (Citus)：以备用方式生成 |
| <B> 区域冗余 | - 可将 Azure VM 设置为在不同的可用性区域中运行。 对于本地解决方案，客户必须创建、管理和维护其自己的辅助数据中心。 | - 单一服务器：否 <br> - 灵活服务器：是 <br> - 超大规模 (Citus)：否 |
| <B> 混合方案 | - 由客户管理 |- 单一服务器：只读副本 <br> - 灵活服务器：预览版中不提供 <br> - 超大规模 (Citus)：否 |
| <B> 备份和还原 | - 由客户管理 | - 单一服务器：内置，具有对本地和异地的用户配置 <br> - 灵活服务器：内置，具有对区域冗余存储的用户配置 <br> - 超大规模 (Citus)：内置 |
| <B> 监视数据库操作 | - 由客户管理 | - 单一服务器、灵活服务器和超大规模 (Citus)：它们都为客户提供了对数据库操作设置警报并在达到阈值时采取行动的功能。 |
| <B> 高级威胁防护 | - 客户必须自行构建此防护。 |- 单一服务器：是 <br> - 灵活服务器：预览版中不提供 <br> - 超大规模 (Citus)：否 |
| <B> 灾难恢复 | - 由客户管理 | - 单一服务器：异地冗余备份和异地只读副本 <br> - 灵活服务器：预览版中不提供 <br> - 超大规模 (Citus)：否 |
| <B> 智能性能 | - 由客户管理 | - 单一服务器：是 <br> - 灵活服务器：预览版中不提供 <br> - 超大规模 (Citus)：否 |

## <a name="total-cost-of-ownership-tco"></a>总拥有成本 (TCO)

TCO 通常是确定用于托管数据库的最佳解决方案的主要考虑因素。 无论你是现金不足的创业公司，或是在预算严格受限的情况下运作现有公司的团队，都存在这种情况。 本节介绍 Azure 中的计费和许可基础知识，这些知识适用于 Azure Database for PostgreSQL 和 Azure VM 上的 PostgreSQL。

## <a name="billing"></a>计费

Azure Database for PostgreSQL 当前以服务的形式在多个层级提供，各个层级的资源价格不同。 所有资源都按固定费率按小时计费。 有关当前支持的服务层级、计算大小和存储量的最新信息，请参阅[定价页面](https://azure.microsoft.com/pricing/details/postgresql/server/)。可以动态调整服务层级和计算大小，以满足应用程序的各种吞吐量需求。 你需要按一般的[数据传输费率](https://azure.microsoft.com/pricing/details/data-transfers/)支付 Internet 流量传出费用。

借助 Azure Database for PostgreSQL，Microsoft 自动配置、修补和升级数据库软件。 这些自动化操作可以降低管理成本。 此外，Azure Database for PostgreSQL 具有[自动备份链接]()功能。 这些功能可帮助你大幅节省成本，尤其是存在大量的数据库时。 相比之下，通过 Azure VM 上的 PostgreSQL，可以选择并运行任何 PostgreSQL 版本。 但是，你需要为预配的 VM、与数据关联的存储成本、备份、监视数据和日志存储以及所使用的特定 PostgreSQL 许可证类型（如果有）的成本付费。

Azure Database for PostgreSQL 为任何类型的节点级中断提供内置的高可用性，同时仍维持了 99.99% 的 SLA 保证。 但是，若要在 VM 中实现数据库高可用性，请使用 PostgreSQL 数据库上可用的高可用性选项，例如[流式处理复制](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION)。 使用支持的高可用性选项不会提供额外的 SLA。 但是，它可以让你凭借额外的成本和管理开销实现 99.99% 以上的数据库可用性。

有关定价的详细信息，请参阅以下文章：
- [Azure Database for PostgreSQL 定价](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>管理

对许多企业来说，决定过渡到到云服务的关键在于降低管理复杂度，因为这涉及到成本。

借助 IaaS，Microsoft 可以实现以下目的：

- 管理底层基础结构。
- 为基础硬件和 OS 提供自动修补

借助 PaaS，Microsoft 可以实现以下目的：

- 管理底层基础结构。
- 为基础硬件、OS 和数据库引擎提供自动修补。
- 管理数据库的高可用性。
- 自动执行备份并复制所有数据以提供灾难恢复。
- 默认加密静态数据和动态数据。
- 监视服务器并提供查询性能见解和性能建议的功能。

使用 Azure Database for PostgreSQL，可以继续管理数据库。 但是，不再需要管理数据库引擎、操作系统或硬件。 可以持续管理的项的示例包括：

- 数据库
- 登录
- 索引优化
- 查询优化
- 审核
- 安全性

此外，在另一个数据中心配置高可用性只需极少量的配置或管理，或者根本无需配置或管理。

- 使用 Azure VM 上的 PostgreSQL，可以完全控制操作系统和 PostgreSQL 服务器实例配置。 使用 VM，可以决定何时更新或升级操作系统和数据库软件，以及应用哪些补丁。 还可以决定何时安装任何其他软件，例如防病毒应用程序。 提供的某些自动化功能可以大大简化修补、备份和高可用性。 可以控制 VM 的大小、磁盘数目及其存储配置。 有关详细信息，请参阅 [Azure 的虚拟机和云服务大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>何时迁移到 Azure PostgreSQL Service (PaaS)

- 当开发人员工作效率和新解决方案的快速面市时间至关重要时，Azure Database for PostgreSQL 是适用于云设计应用程序的理想解决方案。 该服务提供类似于 DBA 的编程功能，非常适合云架构师和开发人员，因为它能降低管理底层操作系统和数据库的需求。

- 如果你不想花费时间和金钱获取新的本地硬件，Azure VM 上的 PostgreSQL 是适用于满足以下条件的应用程序的理想解决方案：这些应用程序需要对服务不支持的 PostgreSQL 引擎进行精细控制和自定义，或者需要访问基础 OS。

## <a name="next-steps"></a>后续步骤

- 请参阅 Azure Database for [PostgreSQL 定价](https://azure.microsoft.com/pricing/details/postgresql/server/)。
- 从创建第一个服务器开始。


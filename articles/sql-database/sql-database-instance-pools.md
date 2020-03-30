---
title: 实例池（预览）
description: 本文介绍 Azure SQL 数据库实例池（预览）。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: c1e740fbfa4bf1e8a77a2d9d6060ab39dba7ae7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256206"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>什么是 SQL 数据库实例池（预览）？

实例池是 Azure SQL 数据库中的新资源，它提供了一种方便且经济高效的方法，用于大规模将较小的 SQL 实例迁移到云。

可以通过实例池按照总的迁移要求对计算资源进行预配置。 然后即可根据预配置的计算级别部署多个单独的托管实例。 例如，如果预先预配 8 个 vCore，则可以部署两个 2 vCore 和一个 4 个 vCore 实例，然后将数据库迁移到这些实例中。 在实例池可用之前，在迁移到云时，通常必须将更小且计算密集型工作负载合并到较大的托管实例中。 需要将数据库组迁移到大型实例通常需要仔细的容量规划和资源治理、额外的安全注意事项以及实例级别的一些额外的数据整合工作。

此外，实例池支持本机 VNet 集成，因此您可以在同一子网中部署多个实例池和多个单个实例。


## <a name="key-capabilities-of-instance-pools"></a>实例池的关键功能

实例池提供以下优势：

1. 能够承载 2 个 vCore 实例。 *仅适用于实例池中的实例。 \**
2. 可预测且快速的实例部署时间（最多 5 分钟）。
3. 最少的 IP 地址分配。

下图演示了一个实例池，该实例池在虚拟网络子网中部署了多个实例。

![具有多个实例的实例池](./media/sql-database-instance-pools/instance-pools1.png)

实例池允许在同一虚拟机上部署多个实例，其中虚拟机的计算大小基于为池分配的 vCore 的总数。 此体系结构允许将虚拟机*分区*为多个实例，这些实例可以是任何受支持的大小，包括 2 个 vCore（2 个 vCore 实例仅适用于池中的实例）。

最初部署池后，对池中的实例的管理操作要快得多。 这些操作更快，因为部署或扩展[虚拟群集](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)（专用虚拟机集）不是预配托管实例的一部分。

由于池中的所有实例都共享同一虚拟机，因此总 IP 分配不依赖于部署的实例数，这便于部署在 IP 范围狭窄的子网中。

每个池的固定 IP 分配只有九个 IP 地址（不包括子网中保留的五个 IP 地址，这些地址是为其自身需要保留的）。 有关详细信息，请参阅[单个实例的子网大小要求](sql-database-managed-instance-determine-size-vnet-subnet.md)。

## <a name="application-scenarios-for-instance-pools"></a>实例池的应用程序方案

以下列表提供了应考虑实例池的主要用例：

- 同时迁移*一组 SQL 实例*，其中大多数大小较小（例如 2 或 4 个 vCore）。
- *可预测和短期实例创建或缩放*很重要的方案。 例如，在需要实例级功能的多租户 SaaS 应用程序环境中部署新租户。
- 具有*固定成本*或*支出限制*的方案非常重要。 例如，运行具有固定（或不经常更改）大小的共享开发测试或演示环境，在环境中，您可以在需要时定期部署托管实例。
- VNet 子网中*最少 IP 地址分配*很重要的方案。 池中的所有实例都在共享虚拟机，因此分配的 IP 地址数低于单个实例。


## <a name="architecture-of-instance-pools"></a>实例池的体系结构

实例池具有与常规托管实例（*单个实例*）类似的体系结构。 为了支持 [Azure 虚拟网络 （VNet）](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) 中的部署，并为客户提供隔离和安全，实例池还依赖于 [虚拟群集](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)。 虚拟群集表示部署在客户虚拟网络子网内的专用隔离虚拟机集。

这两种部署模型之间的主要区别是实例池允许在同一虚拟机节点上进行多个 SQL Server 进程部署，该部署使用[Windows 作业对象](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects)进行资源管理，而单个实例始终单独在虚拟机节点上。

下图显示了实例池和部署在同一子网中的两个单独的实例，并说明了两个部署模型的主要体系结构详细信息：

![实例池和两个单独的实例](./media/sql-database-instance-pools/instance-pools2.png)

每个实例池在下方创建一个单独的虚拟群集。 池中的实例和部署在同一子网中的单个实例不共享分配给 SQL Server 进程和网关组件的计算资源，这可确保性能可预测性。

## <a name="instance-pools-resource-limitations"></a>实例池资源限制

有多个针对实例池和池中实例的资源限制：

- 实例池仅在 Gen5 硬件上可用。
- 池中的实例具有专用 CPU 和 RAM，因此所有实例的 vCore 的聚合数量必须小于或等于分配给池的 vCore 数。
- 所有[实例级别限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)都适用于在池中创建的实例。
- 除了实例级限制外，*在实例池级别*也规定了两个限制：
  - 每个池的总存储大小（8 TB）。
  - 每个池的数据库总数 （100）。

所有实例的总存储分配和数据库数必须低于或等于实例池公开的限制。

- 实例池支持 8、16、24、32、40、64 和 80 个 vCore。
- 池内的托管实例支持 2、4、8、16、24、32、40、64 和 80 vCore。
- 池内的托管实例支持 32 GB 和 8 TB 之间的存储大小，但：
  - 2 个 vCore 实例支持 32 GB 和 640 GB 之间的大小
  - 4 个 vCore 实例支持 32 GB 和 2 TB 之间的大小

[服务层属性](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)与实例池资源相关联，因此池中的所有实例必须与池的服务层相同。 此时，只有通用服务层可用（请参阅以下有关当前预览中限制的部分）。

### <a name="public-preview-limitations"></a>公共预览版限制

公共预览版有以下限制：

- 目前，只有通用服务层可用。
- 在公共预览期间无法缩放实例池，因此在部署之前必须仔细规划容量。
- 实例池创建和配置的 Azure 门户支持尚不可用。 仅通过 PowerShell 支持实例池上的所有操作。 仅通过 PowerShell 支持预创建池中的初始实例部署。 部署到池后，可以使用 Azure 门户更新托管实例。
- 在池外创建的托管实例无法移动到现有池中，在池内创建的实例不能作为单个实例移动到外部，也不能移动到另一个池。
- 保留实例定价（包含许可证或与 Azure 混合权益一起）不可用。

## <a name="sql-features-supported"></a>支持的 SQL 功能

在池中创建的实例支持[单个托管实例中支持的相同兼容性级别和功能](sql-database-managed-instance.md#sql-features-supported)。

在池中部署的每个托管实例都有一个单独的 SQL 代理实例。

需要您选择特定值的可选功能或功能（如实例级排序规则、时区、数据流量的公共终结点、故障转移组）在实例级别进行配置，并且对于池中的每个实例可以不同。

## <a name="performance-considerations"></a>性能注意事项

尽管池中的托管实例确实具有专用 vCore 和 RAM，但它们共享本地磁盘（用于 tempdb 使用）和网络资源。 这不太可能，但如果池中的多个实例同时具有高资源消耗，则可能会体验*嘈杂的邻居*效果。 如果观察到此行为，请考虑将这些实例部署到更大的池或单个实例。

## <a name="security-considerations"></a>安全注意事项

由于部署在池中的实例共享相同的虚拟机，因此您可能需要考虑禁用引入更高安全风险的功能，或者牢牢控制对这些功能的访问权限。 例如，CLR 集成、本机备份和还原、数据库电子邮件等。

## <a name="instance-pool-support-requests"></a>实例池支持请求

在[Azure 门户](https://portal.azure.com)中创建和管理实例池的支持请求。

如果遇到与实例池部署（创建或删除）相关的问题，请确保在 **"问题子类型"** 字段中指定**实例池**。

![实例池支持请求](./media/sql-database-instance-pools/support-request.png)

如果遇到与池中的单个实例或数据库相关的问题，则应为 Azure SQL 数据库托管实例创建常规支持票证。

要创建更大的托管实例部署（无论是否具有实例池），您可能需要获取更大的区域配额。 有关详细信息，请参阅请求[Azure SQL 数据库的配额增加](quota-increase-request.md)。 请注意，如果使用实例池，部署逻辑会将*池级别的总*vCore 消耗量与配额进行比较，以确定是否允许在不进一步增加配额的情况下创建新资源。

## <a name="instance-pool-billing"></a>实例池计费

实例池允许独立扩展计算和存储。 客户为与 vCore 中测量的池资源关联的计算付费，以及与以千兆字节为单位的每个实例关联的存储（每个实例都是免费的前 32 GB）。

无论池中部署了多少个实例，都会收取池的 vCore 价格。

对于计算价格（以 vCore 为单位），有两个定价选项可用：

  1. *包含许可证*：包括 SQL 许可证的价格。 这是为选择不使用软件保障应用现有 SQL Server 许可证的客户。
  2. *Azure 混合权益*：包括 SQL Server 的 Azure 混合权益的降低价格。 客户可以通过使用其现有的 SQL Server 许可证（以及软件保障）选择此价格。 有关资格和其他详细信息，请参阅[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)。

池中的单个实例无法设置不同的定价选项。 父池中的所有实例必须以许可证包含价格或 Azure 混合权益价格。 创建池后，可以更改池的许可证模型。

> [!IMPORTANT]
> 如果为与池中的实例不同的实例指定许可证模型，则使用池价格并忽略实例级别值。

如果在[符合开发测试权益的订阅](https://azure.microsoft.com/pricing/dev-test/)上创建实例池，则会自动在 Azure SQL 托管实例上收到高达 55% 的折扣率。

有关实例池定价的完整详细信息，请参阅[托管实例定价页上](https://azure.microsoft.com/pricing/details/sql-database/managed/)的*实例池*部分。

## <a name="next-steps"></a>后续步骤

- 要开始使用实例池，请参阅[SQL 数据库实例池如何指导](sql-database-instance-pools-how-to.md)。
- 要了解如何创建第一个托管实例，请参阅[快速入门指南](sql-database-managed-instance-get-started.md)。
- 有关功能和比较列表，请参阅 [SQL 常用功能](sql-database-features.md)。
- 有关 VNet 配置的详细信息，请参阅[托管实例 VNet 配置](sql-database-managed-instance-connectivity-architecture.md)。
- 有关创建托管实例以及从备份文件还原数据库的快速入门，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
- 有关使用 Azure 数据库迁移服务 (DMS) 进行迁移的教程，请参阅[使用 DMS 进行托管实例迁移](../dms/tutorial-sql-server-to-managed-instance.md)。
- 有关使用内置故障排除智能对托管实例数据库性能的高级监视，请参阅[使用 Azure SQL 分析监视 Azure SQL 数据库](../azure-monitor/insights/azure-sql.md)。
- 有关定价信息，请参阅[SQL 数据库托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。

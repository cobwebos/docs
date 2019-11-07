---
title: Azure SQL 数据库实例池（预览）
description: 本文介绍 Azure SQL 数据库实例池（预览版）。
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
ms.openlocfilehash: 7d8c316d5c78cfe09bcf134b5a5c513e1c007d74
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689766"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>什么是 SQL 数据库实例池（预览版）？

实例池是 Azure SQL 数据库中的新资源，可提供一种方便且经济高效的方式，将较小的 SQL 实例大规模迁移到云。

可以通过实例池按照总的迁移要求对计算资源进行预配置。 然后即可根据预配置的计算级别部署多个单独的托管实例。 例如，如果预先预配8个 Vcore，则可以部署两个 2 vCore 和1个 4 vCore 实例，然后将数据库迁移到这些实例。 在实例池可用之前，更小且更少计算密集型工作负荷常常需要在迁移到云时合并到更大的托管实例。 需要将数据库组迁移到大型实例通常需要仔细的容量规划和资源监管、附加的安全注意事项，以及一些额外的数据合并在实例级别工作。

此外，实例池支持本机 VNet 集成，因此你可以在同一子网中部署多个实例池和多个单个实例。


## <a name="key-capabilities-of-instance-pools"></a>实例池的主要功能

实例池具有以下优势：

1. 能够托管2个 vCore 实例。 *仅\*实例池中的实例*。
2. 可预测的实例部署时间（最长5分钟）。
3. 最小 IP 地址分配。

下图说明了一个在虚拟网络子网中部署了多个实例的实例池。

![具有多个实例的实例池](./media/sql-database-instance-pools/instance-pools1.png)

实例池允许在虚拟机的计算大小基于为池分配的 Vcore 总数的同一虚拟机上部署多个实例。 此体系结构允许将虚拟机*分区*为多个实例，这些实例可以是任何受支持的大小，包括2个 vcore （2个 vCore 实例仅适用于池中的实例）。

最初部署池后，对池中实例的管理操作的速度要快得多。 这些操作的速度更快，因为[虚拟群集](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)的部署或扩展（一组专用虚拟机）不是预配托管实例的一部分。

因为池中的所有实例共享相同的虚拟机，所以，总 IP 分配不依赖于部署的实例数，因此，在具有较窄 IP 范围的子网中部署非常方便。

每个池都有一个仅有九个 IP 地址的固定 IP 分配（不包括子网中的五个 IP 地址，这些地址是为自己的需要保留的）。 有关详细信息，请参阅[单一实例的子网大小要求](sql-database-managed-instance-determine-size-vnet-subnet.md)。

## <a name="application-scenarios-for-instance-pools"></a>实例池的应用程序方案

以下列表提供了应考虑实例池的主要用例：

- 同时迁移*一组 SQL 实例*，其中多数是较小的大小（例如2个或4个 vcore）。
- *可预测和短实例创建或缩放*的情况非常重要。 例如，在需要实例级功能的多租户 SaaS 应用程序环境中部署新租户。
- 具有*固定成本*或*支出限制*的情况非常重要。 例如，运行固定（或不常更改）大小的共享开发测试或演示环境，在需要时定期部署托管实例。
- VNet 子网中的*最小 IP 地址分配*非常重要的方案。 池中的所有实例都共享一个虚拟机，因此，分配的 IP 地址的数量低于单个实例的情况。


## <a name="architecture-of-instance-pools"></a>实例池的体系结构

实例池与常规托管实例（*单一实例*）具有相似的体系结构。 为了支持 [Azure 虚拟网络（vnet）中的部署](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) 并为客户提供隔离和安全性，实例池还依赖于 [虚拟群集](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)。 虚拟群集表示在客户的虚拟网络子网中部署的一组专用虚拟机。

这两个部署模型之间的主要区别在于，实例池允许多个 SQL Server 处理同一虚拟机节点上的部署（这是使用[Windows 作业对象](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects)进行资源管理的资源），而单一实例则始终独立于虚拟机节点。

下图显示了在同一子网中部署的实例池和两个单独的实例，并阐释了这两种部署模型的主要体系结构详细信息：

![实例池和两个单独的实例](./media/sql-database-instance-pools/instance-pools2.png)

每个实例池在下面创建一个单独的虚拟群集。 池内的实例和同一子网中部署的单个实例不共享分配给 SQL Server 进程和网关组件的计算资源，这可确保性能可预测性。

## <a name="instance-pools-resource-limitations"></a>实例池资源限制

有多个针对实例池和池中实例的资源限制：

- 实例池仅在 Gen5 硬件上可用。
- 池中的实例具有专用的 CPU 和 RAM，因此，所有实例中的 Vcore 聚合数量必须小于或等于分配给池的 Vcore 数。
- 所有[实例级别限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)都适用于在池中创建的实例。
- 除了实例级别限制之外，还会在*实例池级别*施加两个限制：
  - 每个池的总存储大小（8 TB）。
  - 每个池的数据库总数（100）。

存储分配总数和所有实例中的数据库数量必须小于或等于实例池公开的限制。

- 实例池支持8、16、24、32、40、64和 80 Vcore。
- 池中的托管实例支持2、4、8、16、24、32、40、64和 80 Vcore。
- 池内的托管实例支持 32 GB 到 8 TB 的存储大小，只不过：
  - 2 vCore 实例支持 32 GB 到 640 GB 的大小
  - 4 vCore 实例支持 32 GB 和 2 TB 的大小

[服务层属性](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)与实例池资源关联，因此池中的所有实例必须与池的服务层相同。 目前，只有常规用途的服务层可用（有关当前预览中的限制，请参阅以下部分）。

### <a name="public-preview-limitations"></a>公共预览版限制

公共预览版具有以下限制：

- 目前，只有常规用途的服务层可用。
- 在公共预览版期间，无法缩放实例池，因此在部署非常重要之前，请仔细规划容量。
- 尚未提供用于创建和配置实例池的 Azure 门户支持。 仅通过 PowerShell 支持对实例池的所有操作。 预先创建的池中的初始实例部署也仅通过 PowerShell 支持。 部署到池中后，可以使用 Azure 门户更新托管实例。
- 不能将在池之外创建的托管实例移到现有池，并且不能将在池中创建的实例作为单个实例或另一个池移出。
- 保留实例定价（包含的许可证或 Azure 混合权益）不可用。

## <a name="sql-features-supported"></a>支持的 SQL 功能

在池中创建的实例支持[单一托管实例中支持的相同兼容级别和功能](sql-database-managed-instance.md#sql-features-supported)。

池中部署的每个托管实例都有一个单独的 SQL 代理实例。

要求你选择特定值的可选功能或功能（例如，实例级别的排序规则、时区、数据流量的公共终结点、故障转移组）在实例级别配置，并且对于池中的每个实例都可能不同。

## <a name="performance-considerations"></a>性能注意事项

尽管池中的托管实例有专用的 vCore 和 RAM，但它们共享本地磁盘（用于 tempdb 使用情况）和网络资源。 这不太可能，但如果池中的多个实例同时消耗大量资源，则可能会遇到干扰性的*邻居*影响。 如果观察到此行为，请考虑将这些实例部署到更大的池或单个实例。

## <a name="security-considerations"></a>安全注意事项

由于部署在池中的实例共享相同的虚拟机，因此你可能需要考虑禁用导致更高安全风险的功能，或严格控制对这些功能的访问权限。 例如，CLR 集成、本机备份和还原、数据库电子邮件等。

## <a name="instance-pool-support-requests"></a>实例池支持请求

为[Azure 门户](https://portal.azure.com)中的实例池创建和管理支持请求。

如果遇到与实例池部署（创建或删除）相关的问题，请确保在 "**问题子类型**" 字段中指定**实例池**。

![实例池支持请求](./media/sql-database-instance-pools/support-request.png)

如果遇到与池中的单个实例或数据库相关的问题，则应为 Azure SQL 数据库托管实例创建常规支持票证。

若要创建更大的托管实例部署（具有或不具有实例池），可能需要获得更大的区域配额。 使用[标准托管实例过程请求更大的配额](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance)，但请注意，如果使用的是实例池，则部署逻辑会*将池级别的*vCore 总消耗量与配额进行比较，以确定是否允许创建新资源，而无需进一步增加配额。

## <a name="instance-pool-billing"></a>实例池计费

实例池允许单独缩放计算和存储。 客户需要支付与 Vcore 中度量的池资源关联的计算，以及与每个实例32（以 gb 为单位）关联的存储。

无论在该池中部署多少个实例，都将对池的 vCore 价格收费。

对于计算价格（以 Vcore 度量），提供两个定价选项：

  1. *包含的许可证*：将现有 SQL Server 许可证应用于软件保障。
  2. *Azure 混合权益*：为 SQL Server 提供 Azure 混合权益的减少价格。 客户可以通过使用具有软件保障的现有 SQL Server 许可证来选择此价格。 有关资格和其他详细信息，请参阅[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)。

对于池中的单个实例，不能设置不同的定价选项。 父池中的所有实例都必须是许可证附带价格或 Azure 混合权益价格。 创建池后，可以更改池的许可证模型。

> [!IMPORTANT]
> 如果为实例指定的许可证模型不同于池中的值，则使用池价格，并忽略实例级别值。

如果在可[用于开发测试权益的订阅](https://azure.microsoft.com/pricing/dev-test/)上创建实例池，则会在 Azure SQL 托管实例上自动获得最高55% 的折扣率。

有关实例池定价的完整详细信息，请参阅[托管实例定价页](https://azure.microsoft.com/pricing/details/sql-database/managed/)上的*实例池*部分。

## <a name="next-steps"></a>后续步骤

- 若要开始处理实例池，请参阅[SQL 数据库实例池操作指南](sql-database-instance-pools-how-to.md)。
- 若要了解如何创建第一个托管实例，请参阅[快速入门指南](sql-database-managed-instance-get-started.md)。
- 有关功能和比较列表，请参阅 [SQL 常用功能](sql-database-features.md)。
- 有关 VNet 配置的详细信息，请参阅[托管实例 VNet 配置](sql-database-managed-instance-connectivity-architecture.md)。
- 有关创建托管实例以及从备份文件还原数据库的快速入门，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
- 有关使用 Azure 数据库迁移服务 (DMS) 进行迁移的教程，请参阅[使用 DMS 进行托管实例迁移](../dms/tutorial-sql-server-to-managed-instance.md)。
- 有关内置故障排除智能的高级监视托管实例数据库性能，请参阅[使用 Azure SQL Analytics 监视 AZURE SQL 数据库](../azure-monitor/insights/azure-sql.md)。
- 有关定价信息，请参阅 [SQL 数据库托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
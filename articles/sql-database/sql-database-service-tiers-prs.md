---
title: Azure SQL 数据库“高级 RS”服务层级停用 | Microsoft Docs
description: “高级 RS”服务层级即将停用，对其的支持即将终结 - 请参阅迁移选项。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: d2637618ea9e2a0a0d0369eddce01fae073be221
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566635"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Azure SQL 数据库“高级 RS”服务层级（预览版）即将停用 - 迁移选项

2018 年 2 月，Microsoft 已宣布不会发布 Azure SQL 数据库中“高级 RS”服务层级的正式版，并且在 2019 年 1 月 31 日之后不再为其提供支持。 支持截止时间已推迟到 2019 年 6 月 30 日。 本文介绍从“高级 RS”服务层级迁移到另一服务层级的选项。 2019 年 6 月 30 日之后，Microsoft 会自动将“高级 RS”数据库迁移到最符合“高级 RS”数据库性能要求的正式版服务层级。

以下是可能适用于 Premium RS 客户的迁移目标和定价选项：

- vCore 服务层级

  [基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)中的“常规用途”和“业务关键”服务层级。 这两个服务层级已推出正式版。 基于 vCore 的购买模型还提供了**超大规模**服务层, 可根据每个数据库自动扩展到 100 TB, 按需适应工作负荷需求。 “超大规模”服务层级提供的 IO 性能与[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)中的“高级”服务层级相当，价格与“高级 RS”服务层级比较接近。
- 开发/测试定价

  与 Visual Studio 订阅的许可证涵盖费率相比，[开发/测试定价](https://azure.microsoft.com/pricing/dev-test/)最多可将成本节省 55%。
- Azure 混合权益和预留容量定价

  与许可证涵盖费率相比，[Azure 混合权益和预留容量定价](https://azure.microsoft.com/pricing/details/sql-database/)最多可将成本节省 80%。 有关这些选项的详细信息，请参阅[面向 SQL Server 的 Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)和 [Azure SQL 数据库预留容量](sql-database-reserved-capacity.md)。

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>立即将“高级 RS”数据库迁移到备用的 SQL 数据库服务层级

请查看本文中的指导以及相关定价和文档，以确定 Premium RS 工作负荷的适当迁移目标。

## <a name="migrate-compute-intensive-workloads-and-save"></a>迁移计算密集型工作负荷并实现成本节省

对于计算密集型“高级 RS”工作负荷，我们建议迁移到基于 vCore 的正式版“常规用途”服务层级，并使用面向 SQL Server 的 Azure 混合权益和预留容量产品/服务，以优于许可证涵盖费率的价格实现更大的节省。 如果想要保留使用基于 DTU 的购买选项，可将计算密集型“高级 RS”数据库迁移到“标准”服务层级，与采用“高级 RS”正式版定价（如果已发布正式版）相比，这样仍可以实现节省。

> [!WARNING]
> 与采用当前的“高级 RS”定价相比，将“高级 RS”工作负荷迁移到基于 DTU 的“高级”服务层级可能会增大每月成本。 我们建议考虑使用提供 Azure 混合权益和预留容量定价的“超大规模”或“业务关键”层，以维持与 Premium RS 类似或更低的成本。

### <a name="premium-rs-databases"></a>Premium RS 数据库

|**如果当前使用…**|**迁移到对等的基于 vCore 的服务层...**|**迁移到对等的基于 DTU 的服务层...**|
|---|---|---|
|Premium RS 1|常规用途 1 vCore（第 4 代）|标准 3|
|Premium RS 2|常规用途 2 vCore（第 4 代）|标准 4|
|Premium RS 4|常规用途 4 vCore（第 4 代）|标准 6|
|Premium RS 6|常规用途 6 vCore（第 4 代）|标准 7|

### <a name="premium-rs-pools"></a>Premium RS 池

|**如果当前使用…**|**迁移到对等的基于 vCore 的服务层...**|**迁移到对等的基于 DTU 的服务层...**|
|---|---|---|
|Premium RS 池 125 DTU|常规用途 1 vCore（第 4 代）|标准池 100 eDTU|
|Premium RS 池 250 DTU|常规用途 2 vCore（第 4 代）|标准池 250 eDTU|
|Premium RS 池 500 DTU|常规用途 4 vCore（第 4 代）|标准池 500 eDTU|
|Premium RS 池 1000 DTU|常规用途 8 vCore（第 4 代）|标准池 1000 eDTU|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>优化 IO 密集型工作负荷的成本和性能

我们建议将 IO 密集型单一数据库迁移到基于 vCore 的“超大规模”层（目前为预览版），将 IO 密集型数据库池迁移到正式版“业务关键”层，以同时获得最佳性能和成本。  以下基于 vCore 的选项将会维持或改善当前性能，如果将其与 Azure 混合权益和预留容量定价结合使用，则还可以节省成本。

|**如果当前使用…**|**迁移到对等的基于 vCore 的服务层...**|**迁移到对等的基于 DTU 的服务层...**|
|---|---|---|
|Premium RS 1| 超大规模 1 vCore (Gen4) 或业务关键 1 vCore (Gen4)|高级 1|
|Premium RS 2| 超大规模 2 Vcore (Gen4) 或业务关键 2 Vcore (Gen4|高级 2|
|Premium RS 4| 超大规模 4 Vcore (Gen4) 或业务关键 4 Vcore (Gen4)|高级 4
|Premium RS 6| 超大规模 6 Vcore (Gen4) 或业务关键 6 Vcore (Gen4)|高级 6|

|**如果当前使用…**|**迁移到对等的基于 vCore 的服务层...**|**迁移到对等的基于 DTU 的服务层...**|
|---|---|---|
|Premium RS 池 125 DTU|业务关键 2 vCore（第 4 代）|高级池 125 eDTU|
|Premium RS 池 250 DTU|业务关键 2 vCore（第 4 代）|高级池 250 eDTU|
|Premium RS 池 500 DTU|业务关键 4 vCore（第 4 代）|高级池 500 eDTU|
|Premium RS 池 1000 DTU|业务关键 8 vCore（第 4 代）|高级池 1000 eDTU|

## <a name="take-advantage-of-our-new-offers"></a>利用我们的新产品/服务

基于 vCore 的购买模型中的服务层级享受特别优惠，与许可证涵盖的定价相比，最多可将成本节省 80%。 与附带[面向 SQL Server 的 Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)的许可证涵盖定价相比，附带积极软件保障的 SQL Server Standard 或 Enterprise 版许可证最多可将成本节省 55%。 可将混合权益和 [Azure SQL 数据库预留容量](sql-database-reserved-capacity.md)定价相结合，在提前承诺使用一年或三年时，最多将成本节省 80%。  立即从 Azure 门户激活两项权益。

如果对此项变更有任何疑问或疑虑，或者如需迁移的帮助，请联系 [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>从“高级 RS”服务层级迁移到 DTU 或 vCore 模型中的服务层级

### <a name="migration-of-a-database"></a>迁移数据库

将数据库从“高级 RS”服务层级迁移到 DTU 或 vCore 模型中的服务层级类似于在“高级 RS”服务层级之间升级或降级。

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>使用数据库复制将 Premium RS 数据库转换为基于 DTU 或基于 vCore 的数据库

可将采用 Premium RS 计算大小的任何数据库复制到采用基于 DTU 或基于 vCore 的计算大小的数据库，且无需遵守上述限制或特殊顺序，前提是目标计算大小支持源数据库的最大数据库大小。 数据库复制会在复制操作启动时创建数据快照，且不会在源数据库与目标数据库之间执行数据同步。

## <a name="next-steps"></a>后续步骤

- 有关适用于单一数据库的特定计算大小和存储大小选项的详细信息，请参阅[适用于单一数据库的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 若要详细了解适用于弹性池的特定计算大小和存储大小选项，请参阅[适用于弹性池的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)。

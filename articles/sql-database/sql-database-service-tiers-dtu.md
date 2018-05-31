---
title: Azure SQL 数据库服务 - DTU | Microsoft Docs
description: 了解单一数据库和池数据库的服务层以提供性能级别和存储大小。
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/09/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 6e282291a6e6e219bb275dd4da91f3815590adc1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32196000"
---
# <a name="dtu-based-purchasing-model-for-azure-sql-database"></a>Azure SQL 数据库的基于 DTU 的购买模型 


[Azure SQL 数据库](sql-database-technical-overview.md)为计算、存储和 IO 资源提供两种购买模型：基于 DTU 的购买模型和基于 vCore 的购买模型（预览版）。 以下表格和图表对这两种购买模型做了对比。

> [!IMPORTANT]
> 若要了解基于 vCore 的购买模型（预览版），请参阅[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)


|**购买模型**|**说明**|**最适用于**|
|---|---|---|
|基于 DTU 的模型|此模型基于计算、存储和 IO 资源的捆绑度量。 单一数据库的性能级别以数据库事务单位 (DTU) 表示，弹性池则以弹性数据库事务单位 (eDTU) 表示。 有关 DTU 和 eDTU 的详细信息，请参阅[什么是 DTU 和 eDTU？](sql-database-what-is-a-dtu.md)|最适合希望获得简单预配置资源选项的客户。| 
|基于 vCore 的模型|此模型允许单独缩放计算和存储资源。 此外，它还允许使用面向 SQL Server 的 Azure 混合权益来节省成本。|最适合注重灵活性、控制度和透明度的客户。|
||||  

![定价模型](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>基于 DTU 的购买模型

数据库吞吐量单位 (DTU) 表示 CPU、内存、读取和写入的混合度量。 基于 DTU 的购买模型提供一组预配置的计算资源套件和随附的存储，以促成不同级别的应用程序性能。 偏爱预配置套件简易性和每月定价付款的客户，可能会发现基于 DTU 的模型更适合解决其需求。 在基于 DTU 的购买模型中，客户可为[单一数据库](sql-database-single-database-resources.md)和[弹性池](sql-database-elastic-pool.md)选择“基本”、“标准”或“高级”服务层。 服务层根据一系列具有固定随附存储量、固定备份保留期和固定价格的性能级别进行区分。 所有服务层允许灵活更改性能级别，而不会造成停机。 单一数据库和弹性池根据服务层和性能级别按小时计费。

> [!IMPORTANT]
> SQL 数据库托管实例（目前以公共预览版提供）不支持基于 DTU 的购买模型。 有关详细信息，请参阅 [Azure SQL 数据库托管实例](sql-database-managed-instance.md)。 

## <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>在基于 DTU 的购买模型中选择服务层

选择服务层首要考虑的是业务连续性、存储和性能需求。
||基本|标准|高级|
| :-- | --: |--:| --:| --:| 
|目标工作负荷|开发和生产|开发和生产|开发和生产||
|运行时间 SLA|99.99%|99.99%|99.99%|在预览版中不适用|
|备份保留|7 天|35 天|35 天|
|CPU|低|低、中、高|中、高|
|IO 吞吐量（近似） |每个 DTU 2.5 IOPS| 每个 DTU 2.5 IOPS | 每个 DTU 48 IOPS|
|IO 延迟（近似）|5 毫秒（读取），10 毫秒（写入）|5 毫秒（读取），10 毫秒（写入）|2 毫秒（读取/写入）|
|列存储索引 |不适用|S3 及更高版本|支持|
|内存中 OLTP|不适用|不适用|支持|
|||||

## <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>基于 DTU 的购买模型中的性能级别和存储大小限制

单一数据库的性能级别以数据库事务单位 (DTU) 表示，弹性池则以弹性数据库事务单位 (eDTU) 表示。 有关 DTU 和 eDTU 的详细信息，请参阅[什么是 DTU 和 eDTU？](sql-database-what-is-a-dtu.md)

### <a name="single-databases"></a>单一数据库

||基本|标准|高级|
| :-- | --: | --: | --: | --: |
| 最大存储大小* | 2 GB | 1 TB | 4 TB  | 
| 最大 DTU | 5 | 3000 | 4000 | |
||||||

有关适用于单一数据库的特定性能级别和存储大小选项的详细信息，请参阅[适用于单一数据库的 SQL 数据库基于 DTU 的资源限制](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels)。

### <a name="elastic-pools"></a>弹性池

| | **基本** | **标准** | **高级** | 
| :-- | --: | --: | --: | --: |
| 每个数据库的最大存储大小*  | 2 GB | 1 TB | 1 TB | 
| 每个池的最大存储大小* | 156 GB | 4 TB | 4 TB | 
| 每个数据库的最大 eDTU 数 | 5 | 3000 | 4000 | 
| 每个池的最大 eDTU 数 | 1600 | 3000 | 4000 | 
| 每个池的数据库数目上限 | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> -  超出所包括存储量的存储大小为预览版，需额外付费。 有关详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 
> -  在高级层中，以下区域目前提供的存储超出 1 TB：澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、美国中部、法国中部、德国中部、日本东部、日本西部、韩国中部、美国中北部、北欧、美国中南部、东南亚、英国南部、英国西部、美国东部 2、美国西部、US Gov 弗吉尼亚州和西欧。 请参阅 [P11-P15 当前限制](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  

有关适用于弹性池的特定性能级别和存储大小选项的详细信息，请参阅 [SQL 数据库基于 DTU 的资源限制](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)。



## <a name="next-steps"></a>后续步骤

- 有关特定性能级别和存储大小选项的详细信息，请参阅 [SQL 数据库基于 DTU 的资源限制](sql-database-dtu-resource-limits.md)和 [SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits.md)。
- 有关常见问题的解答，请参阅 [SQL 数据库常见问题解答](sql-database-faq.md)。
- 了解 [Azure 订阅和服务的限制、配额和约束](../azure-subscription-service-limits.md)

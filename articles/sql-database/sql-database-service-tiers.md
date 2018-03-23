---
title: Azure SQL 数据库服务 | Microsoft Docs
description: 了解单一数据库和池数据库的服务层以提供性能级别和存储大小。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 03/15/2018
ms.author: carlrab
ms.openlocfilehash: 6153616de763eee1b20fff40d38816eca8b455de
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>什么是 Azure SQL 数据库服务层？

[Azure SQL 数据库](sql-database-technical-overview.md)可同时向[单一数据库](sql-database-single-database-resources.md)和[弹性池](sql-database-elastic-pool.md)提供“基本”、“标准”和“高级”服务层。 SQL 数据库提供适用于 [Azure SQL 数据库托管实例](sql-database-managed-instance.md#managed-instance-service-tier)的常规用途服务层。 服务层的区别主要在于性能级别、存储大小选择和价格。  所有服务层都能够灵活变动性能级别和存储大小。  单一数据库和弹性池根据服务层、性能级别和存储大小按小时计费。   

> [!IMPORTANT]
> SQL 数据库托管实例（当前处于公共预览状态）提供单个常规用途服务层。 有关详细信息，请参阅 [Azure SQL 数据库托管实例](sql-database-managed-instance.md)。 本文剩余部分不适用于托管实例。

## <a name="choosing-a-service-tier"></a>选择服务层

选择服务层首要考虑的是业务连续性、存储和性能需求。
| | **基本** | **标准** |**高级**  |
| :-- | --: |--:| --:| --:| 
|目标工作负荷|开发和生产|开发和生产|开发和生产||
|运行时间 SLA|99.99%|99.99%|99.99%|在预览版中不适用|
|备份保留|7 天|35 天|35 天|
|CPU|低|低、中、高|中、高|
|IO 吞吐量（近似） |每个 DTU 2.5 IOPS  | 每个 DTU 2.5 IOPS | 每个 DTU 48 IOPS|
|IO 延迟（近似）|5 毫秒（读取），10 毫秒（写入）|5 毫秒（读取），10 毫秒（写入）|2 毫秒（读取/写入）|
|列存储索引和内存中 OLTP|不适用|不适用|支持|
|||||

## <a name="performance-level-and-storage-size-limits"></a>性能级别和存储大小限制

单一数据库的性能级别以数据库事务单位 (DTU) 表示，弹性池则以弹性数据库事务单位 (eDTU) 表示。 有关 DTU 和 eDTU 的详细信息，请参阅[什么是 DTU 和 eDTU？](sql-database-what-is-a-dtu.md)

### <a name="single-databases"></a>单一数据库

|  | **基本** | **标准** | **高级** | 
| :-- | --: | --: | --: | --: |
| 最大存储大小* | 2 GB | 1 TB | 4 TB  | 
| 最大 DTU | 5 | 3000 | 4000 | |
||||||

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
> \* 超出所包括存储量的存储大小为预览版，需额外付费。 有关详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 
>
> \* 在高级层中，以下区域目前提供的存储超出 1 TB：澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、美国中部、法国中部、德国中部、日本东部、日本西部、韩国中部、美国中北部、北欧、美国中南部、东南亚、英国南部、英国西部、美国东部 2、美国西部、美国弗吉尼亚州政府和西欧。 请参阅 [P11-P15 当前限制](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
> 

有关特定性能级别和可选存储大小的详细信息，请参阅 [SQL 数据库资源限制](sql-database-resource-limits.md)。


## <a name="next-steps"></a>后续步骤

- 了解[单一数据库资源](sql-database-single-database-resources.md)。
- 有关弹性池的信息，请参阅[弹性池](sql-database-elastic-pool.md)。
- 了解 [Azure 订阅和服务的限制、配额和约束](../azure-subscription-service-limits.md)
* 详细了解 [DTU 和 eDTU](sql-database-what-is-a-dtu.md)。
* 要了解如何监视 DTU 的使用情况，请参阅[监视和性能优化](sql-database-troubleshoot-performance.md)。


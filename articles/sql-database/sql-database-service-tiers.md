---
title: "Azure SQL 数据库服务 | Microsoft Docs"
description: "了解单一数据库和池数据库的服务层以提供性能级别和存储大小。"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 08/20/2017
ms.author: carlrab
ms.openlocfilehash: 55f59fddee008eb42b7252d6368a56873a6abd16
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="what-are-azure-sql-database-service-tiers"></a>什么是 Azure SQL 数据库服务层

[Azure SQL 数据库](sql-database-technical-overview.md)可同时向[单一数据库](sql-database-single-database-resources.md)和[弹性池](sql-database-elastic-pool.md)提供基本、标准、高级和高级 RS 服务层。 服务层的区别主要在于性能级别、存储大小选择和价格。  所有服务层都能够灵活变动性能级别和存储大小。  单一数据库和弹性池根据服务层、性能级别和存储大小按小时计费。   

## <a name="choosing-a-service-tier"></a>选择服务层

选择服务层首要考虑的是业务连续性、存储和性能需求。
| | **基本** | **标准** |**高级** |**高级 RS** |
| :-- | --: |--:| --:| --:| 
|目标工作负荷|开发和生产|开发和生产|开发和生产|对因服务故障而造成的数据丢失，工作负荷的容错时间可达 5 分钟|
|运行时间 SLA|99.99%|99.99%|99.99%|在预览版中不适用|
|备份保留|7 天|35 天|35 天|35 天|
|CPU|低|低、中、高|中、高|中型|
|IO 吞吐量|低  | 中型 | 数量级高于标准版|与高级版相同|
|IO 延迟|高于高级版|高于高级版|低于基本版和标准版|与高级版相同|
|列存储索引和内存中 OLTP|不适用|不适用|支持|支持|
|||||

## <a name="performance-level-and-storage-size-limits"></a>性能级别和存储大小限制

单一数据库的性能级别以数据库事务单位 (DTU) 表示，弹性池则以弹性数据库事务单位 (eDTU) 表示。 有关 DTU 和 eDTU 的详细信息，请参阅[什么是 DTU 和 eDTU？](sql-database-what-is-a-dtu.md)

### <a name="single-databases"></a>单一数据库

|  | **基本** | **标准** | **高级** | **高级 RS**|
| :-- | --: | --: | --: | --: |
| 最大存储大小* | 2 GB | 1 TB | 4 TB  | 1 TB  |
| 最大 DTU | 5 | 3000 | 4000 | 1000 |
||||||

### <a name="elastic-pools"></a>弹性池

| | **基本** | **标准** | **高级** | **高级 RS**|
| :-- | --: | --: | --: | --: |
| 每个数据库的最大存储大小*  | 2 GB | 1 TB | 1 TB | 1 TB |
| 每个池的最大存储大小* | 156 GB | 4 TB | 4 TB | 1 TB |
| 每个数据库的最大 eDTU 数 | 5 | 3000 | 4000 | 1000 |
| 每个池的最大 eDTU 数 | 1600 | 3000 | 4000 | 1000 |
| 每个池的数据库数目上限 | 500  | 500 | 100 | 100 |
||||||

> [!IMPORTANT]
> \* 超出所包括存储量的存储大小为预览版，需额外付费。 有关详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 
>
> \* 在高级层中，以下区域目前提供的存储超出 1 TB：美国东部 2、美国西部、美国弗吉尼亚州政府、西欧、德国中部、东南亚、日本东部、澳大利亚东部、加拿大中部和加拿大东部。 请参阅 [P11-P15 当前限制](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
> 

有关特定性能级别和可选存储大小的详细信息，请参阅 [SQL 数据库资源限制](sql-database-resource-limits.md)。


## <a name="next-steps"></a>后续步骤

- 了解[单一数据库资源](sql-database-single-database-resources.md)。
- 有关弹性池的信息，请参阅[弹性池](sql-database-elastic-pool.md)。
- 了解 [Azure 订阅和服务的限制、配额和约束](../azure-subscription-service-limits.md)
* 详细了解 [DTU 和 eDTU](sql-database-what-is-a-dtu.md)。
* 要了解如何监视 DTU 的使用情况，请参阅[监视和性能优化](sql-database-troubleshoot-performance.md)。


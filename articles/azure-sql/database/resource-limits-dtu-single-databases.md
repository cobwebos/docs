---
title: DTU 资源限制单一数据库
description: 本页介绍 Azure SQL 数据库中单一数据库的一些常见 DTU 资源限制。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: references_regions, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: 0bcc26c637e8ef6043589d963cfa9708b599a456
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619058"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>使用 DTU 购买模型的单一数据库的资源限制 - Azure SQL 数据库
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文提供了针对使用 DTU 购买模型的 Azure SQL 数据库的单一数据库的详细资源限制。

有关弹性池的 DTU 购买模型资源限制，请参阅 [DTU 资源限制 - 弹性池](resource-limits-dtu-elastic-pools.md)。 有关 vCore 资源限制，请参阅 [vCore 资源限制 - 单一数据库](resource-limits-vcore-single-databases.md)和 [vCore 资源限制 - 弹性池](resource-limits-vcore-elastic-pools.md)。 有关不同购买模型的更多信息，请参阅[购买模型和服务层级](purchasing-models.md)。

## <a name="single-database-storage-sizes-and-compute-sizes"></a>单一数据库：存储大小和计算大小

下表显示了可用于每个服务层级和计算大小的单一数据库的资源。 可通过 [Azure 门户](single-database-manage.md#the-azure-portal)、[Transact-SQL](single-database-manage.md#transact-sql-t-sql)、[PowerShell](single-database-manage.md#powershell)、[Azure CLI](single-database-manage.md#the-azure-cli) 或 [REST API](single-database-manage.md#rest-api) 为单一数据库设置服务层级、计算大小和存储量。

> [!IMPORTANT]
> 有关缩放指南和注意事项，请参阅[缩放单一数据库](single-database-scale.md)

### <a name="basic-service-tier"></a>“基本”服务层级

| **计算大小** | **基本** |
| :--- | --: |
| 最大 DTU 数 | 5 |
| 包含的存储 (GB) | 2 |
| 最大存储 (GB) | 2 |
| 最大内存中 OLTP 存储 (GB) |空值 |
| 最大并发工作线程数（请求数） | 30 |
| 最大并发会话数 | 300 |
|||

> [!IMPORTANT]
> “基本”服务层级提供的 vCore (CPU) 不到一个。  对于 CPU 密集型工作负荷，建议使用 S3 或更高的服务层级。
>
>有关数据存储的“基本”服务层级放置在标准页 Blob 上。 标准页 Blob 使用基于硬盘驱动器 (HDD) 的存储介质，最适合用于对性能变化不太敏感的开发、测试和其他不频繁访问的工作负荷。
>

### <a name="standard-service-tier"></a>“标准”服务层级

| **计算大小** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 最大 DTU | 10 个 | 20 个 | 50 | 100 |
| 包含的存储 (GB) <sup>1</sup> | 250 | 250 | 250 | 250 |
| 最大存储 (GB) | 250 | 250 | 250 | 1024 |
| 最大内存中 OLTP 存储 (GB) | 空值 | 空值 | 空值 | 空值 |
| 最大并发工作线程数（请求数）| 60 | 90 | 120 | 200 |
| 最大并发会话数 |600 | 900 | 1200 | 2400 |
||||||

<sup>1</sup> 请参阅 [SQL 数据库定价选项](https://azure.microsoft.com/pricing/details/sql-database/single/)，详细了解由于任何预配的额外存储导致的额外成本。

> [!IMPORTANT]
> 标准 S0、S1 和 S2 层级提供的 vCore (CPU) 不到一个。  对于 CPU 密集型工作负荷，建议使用 S3 或更高的服务层级。
>
>有关数据存储的标准 S0 和 S1 服务层级放置在标准页 Blob 上。 标准页 Blob 使用基于硬盘驱动器 (HDD) 的存储介质，最适合用于对性能变化不太敏感的开发、测试和其他不频繁访问的工作负荷。
>

### <a name="standard-service-tier-continued"></a>“标准”服务层级（续）

| **计算大小** | **S4** | **S6** | S7 | S9 | S12 |
| :--- |---:| ---:|---:|---:|---:|
| 最大 DTU 数 | 200 | 400 | 800 | 1600 | 3000 |
| 包含的存储 (GB) <sup>1</sup> | 250 | 250 | 250 | 250 | 250 |
| 最大存储 (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
| 最大内存中 OLTP 存储 (GB) | 空值 | 空值 | 空值 | 空值 |空值 |
| 最大并发工作线程数（请求数）| 400 | 800 | 1600 | 3200 |6000 |
| 最大并发会话数 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

<sup>1</sup> 请参阅 [SQL 数据库定价选项](https://azure.microsoft.com/pricing/details/sql-database/single/)，详细了解由于任何预配的额外存储导致的额外成本。

### <a name="premium-service-tier"></a>“高级”服务层级

| **计算大小** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU 数 | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 包含的存储 (GB) <sup>1</sup> | 500 | 500 | 500 | 500 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| 最大存储 (GB) | 1024 | 1024 | 1024 | 1024 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| 最大内存中 OLTP 存储 (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 最大并发工作线程数（请求数）| 200 | 400 | 800 | 1600 | 2800 | 6400 |
| 最大并发会话数 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

<sup>1</sup> 请参阅 [SQL 数据库定价选项](https://azure.microsoft.com/pricing/details/sql-database/single/)，详细了解由于任何预配的额外存储导致的额外成本。

<sup>2</sup> 从 1024 GB 到 4096 GB，以 256 GB 为增量。

> [!IMPORTANT]
> 高级层中超过 1 TB 的存储目前在除：中国东部、中国北部、德国中部和德国东北部以外的所有区域中都可用。 在这些区域，高级层中的最大存储限制为 1 TB。  有关详细信息，请参阅[P11-P15 当前限制](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)。
> [!NOTE]
> 有关 `tempdb` 限制，请参阅 [tempdb 限制](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)。

## <a name="next-steps"></a>后续步骤

- 有关单一数据库的 vCore 资源限制，请参阅[使用 vCore 购买模型的单一数据库的资源限制](resource-limits-vcore-single-databases.md)
- 有关弹性池的 vCore 资源限制，请参阅[使用 vCore 购买模型的弹性池的资源限制](resource-limits-vcore-elastic-pools.md)
- 有关弹性池的 DTU 资源限制，请参阅[使用 DTU 购买模型的弹性池的资源限制](resource-limits-dtu-elastic-pools.md)
- 有关 Azure SQL 托管实例中的托管实例的资源限制，请参阅 [SQL 托管实例资源限制](../managed-instance/resource-limits.md)。
- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 有关逻辑 SQL 服务器上的资源限制的信息，请参阅[逻辑 SQL 服务器资源限制概述](resource-limits-logical-server.md)了解有关服务器级别和订阅级别限制的信息。

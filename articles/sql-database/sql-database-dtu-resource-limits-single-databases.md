---
title: DTU 资源限制单个数据库
description: 本页介绍 Azure SQL 数据库中单一数据库的一些常见 DTU 资源限制。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: 75611dd36196c722254d694e9753deb982e2570d
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539336"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>使用 DTU 购买模型的单一数据库的资源限制

本文提供了有关使用 DTU 购买模型的 Azure SQL 数据库单一数据库的详细资源限制。

有关弹性池的 DTU 购买模型资源限制，请参阅[dtu 资源限制-弹性池](sql-database-dtu-resource-limits-elastic-pools.md)。 有关 vCore 资源限制，请参阅[vCore 资源限制-单数据库](sql-database-vcore-resource-limits-single-databases.md)和[vCore 资源限制-弹性池](sql-database-vcore-resource-limits-elastic-pools.md)。 有关不同购买模型的更多信息，请参阅[购买模型和服务层级](sql-database-purchase-models.md)。

## <a name="single-database-storage-sizes-and-compute-sizes"></a>单一数据库：存储大小和计算大小

下表显示了可用于每个服务层级和计算大小的单一数据库的资源。 可通过 [Azure 门户](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、[Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases)、[PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)、[Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) 或 [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases) 为单一数据库设置服务层级、计算大小和存储量。

> [!IMPORTANT]
> 有关缩放指南和注意事项，请参阅[缩放单一数据库](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>“基本”服务层级

| **计算大小** | **基本** |
| :--- | --: |
| 最大 DTU 数 | 5 |
| 包含的存储 (GB) | 2 |
| 最大存储选择 (GB) | 2 |
| 最大内存中 OLTP 存储 (GB) |不适用 |
| 最大并发工作线程数（请求数） | 30 |
| 最大并发会话数 | 300 |
|||

> [!IMPORTANT]
> 基本服务层提供了少于1个 vCore （CPU）。  对于占用大量 CPU 的工作负荷，建议使用 S3 或更高的服务层。 
>
>对于数据存储，基本服务层位于标准页 Blob 上。 标准页 Blob 使用基于硬盘驱动器（HDD）的存储介质，最适合用于开发、测试和其他不太敏感的不太敏感的工作负载，这些工作负荷对性能变化不太敏感。
>

### <a name="standard-service-tier"></a>“标准”服务层级

| **计算大小** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 最大 DTU 数 | 10 | 20 | 50 | 100 |
| 包含的存储 (GB) | 250 | 250 | 250 | 250 |
| 最大存储选择 (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| 最大内存中 OLTP 存储 (GB) | 不适用 | 不适用 | 不适用 | 不适用 |
| 最大并发工作线程数（请求数）| 60 | 90 | 120 | 200 |
| 最大并发会话数 |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> 标准 S0、S1 和 S2 层提供少于1个 vCore （CPU）。  对于占用大量 CPU 的工作负荷，建议使用 S3 或更高的服务层。 
>
>对于数据存储，标准 S0 和 S1 服务层放置在标准页 Blob 上。 标准页 Blob 使用基于硬盘驱动器（HDD）的存储介质，最适合用于开发、测试和其他不太敏感的不太敏感的工作负载，这些工作负荷对性能变化不太敏感。
>

### <a name="standard-service-tier-continued"></a>“标准”服务层级（续）

| **计算大小** | **S4** | **S6** | S7 | S9 | S12 |
| :--- |---:| ---:|---:|---:|---:|
| 最大 DTU 数 | 200 | 400 | 800 | 1600 | 3000 |
| 包含的存储 (GB) | 250 | 250 | 250 | 250 | 250 |
| 最大存储选择 (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| 最大内存中 OLTP 存储 (GB) | 不适用 | 不适用 | 不适用 | 不适用 |不适用 |
| 最大并发工作线程数（请求数）| 400 | 800 | 1600 | 3200 |6000 |
| 最大并发会话数 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>“高级”服务层级

| **计算大小** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU 数 | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 包含的存储 (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| 最大存储选择 (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| 最大内存中 OLTP 存储 (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 最大并发工作线程数（请求数）| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| 最大并发会话数 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* 从 1024 GB 到 4096 GB，以 256 GB 为增量

> [!IMPORTANT]
> 高级层中的存储空间超过 1 TB 目前在除：中国东部、中国北部、德国中部、德国北部、美国中部、US DoD 地区和美国政府中心以外的所有地区都可用。 在这些区域，高级层中的最大存储限制为 1 TB。  有关详细信息，请参阅[P11-P15 当前限制](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)。  
> [!NOTE]
> 有关 `tempdb` 限制，请参阅 [tempdb 限制](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)。

## <a name="next-steps"></a>后续步骤

- 有关单个数据库的 vCore 资源限制，请参阅[使用 vCore 购买模型的单一数据库的资源限制](sql-database-vcore-resource-limits-single-databases.md)
- 有关弹性池的 vCore 资源限制，请参阅[使用 vCore 购买模型的弹性池的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)
- 有关弹性池的 DTU 资源限制，请参阅[使用 DTU 购买模型的弹性池的资源限制](sql-database-dtu-resource-limits-elastic-pools.md)
- 有关托管实例的资源限制，请参阅[托管实例资源限制](sql-database-managed-instance-resource-limits.md)。
- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-subscription-service-limits.md)。
- 有关数据库服务器上的资源限制的信息，请参阅 [SQL 数据库服务器资源限制概述](sql-database-resource-limits-database-server.md)了解有关服务器级别和订阅级别限制的信息。

---
title: Azure SQL 数据库基于 DTU 的资源限制 - 单一数据库 | Microsoft Docs
description: 本页介绍 Azure SQL 数据库中单一数据库的一些常见资源限制（基于 DTU）。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/20/2019
ms.openlocfilehash: a0b565be2edba7dcf1ba4c1b17284ce05b4cea2f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620540"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>使用基于 DTU 的购买模型的单一数据库的资源限制

本文提供了针对使用基于 DTU 的购买模型的 Azure SQL 数据库的单一数据库的详细资源限制。

有关弹性池的基于 DTU 的购买模型资源限制，请参阅[基于 DTU 的资源限制 - 弹性池](sql-database-dtu-resource-limits-elastic-pools.md)。 有关基于 vCore 的资源限制，请参阅[基于 vCore 的资源限制 - 单一数据库](sql-database-vcore-resource-limits-single-databases.md)和[基于 vCore 的资源限制 - 弹性池](sql-database-vcore-resource-limits-elastic-pools.md)。 有关不同购买模型的更多信息，请参阅[购买模型和服务层](sql-database-purchase-models.md)。

## <a name="single-database-storage-sizes-and-compute-sizes"></a>单一数据库：存储大小和计算大小

下表显示了可用于每个服务层和计算大小的单一数据库的资源。 可通过 [Azure 门户](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、[Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases)、[PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)、[Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) 或 [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases) 为单一数据库设置服务层级、计算大小和存储量。

> [!IMPORTANT]
> 有关缩放指南和注意事项，请参阅[缩放单一数据库](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>基本服务层

| **计算大小** | **基本** |
| :--- | --: |
| 最大 DTU 数 | 5 |
| 包含的存储 (GB) | 2 |
| 最大存储选择 (GB) | 2 |
| 最大内存中 OLTP 存储 (GB) |不适用 |
| 最大并发工作线程数（请求数） | 30 |
| 最大并发会话数 | 300 |
|||

### <a name="standard-service-tier"></a>标准服务层

| **计算大小** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 最大 DTU | 10 | 20 | 50 | 100 |
| 包含的存储 (GB) | 250 | 250 | 250 | 250 |
| 最大存储选择 (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| 最大内存中 OLTP 存储 (GB) | 不适用 | 不适用 | 不适用 | 不适用 |
| 最大并发工作线程数（请求数）| 60 | 90 | 120 | 200 |
| 最大并发会话数 |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>标准服务层（续）

| **计算大小** | **S4** | **S6** | S7 | S9 | S12 |
| :--- |---:| ---:|---:|---:|---:|
| 最大 DTU 数 | 200 | 400 | 800 | 1600 | 3000 |
| 包含的存储 (GB) | 250 | 250 | 250 | 250 | 250 |
| 最大存储选择 (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| 最大内存中 OLTP 存储 (GB) | 不适用 | 不适用 | 不适用 | 不适用 |不适用 |
| 最大并发工作线程数（请求数）| 400 | 800 | 1600 | 3200 |6000 |
| 最大并发会话数 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>高级服务层

| **计算大小** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU 数 | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 包含的存储 (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| 最大存储选择 (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| 最大内存中 OLTP 存储 (GB) | 第 | 2 | 4 | 8 | 14 | 32 |
| 最大并发工作线程数（请求数）| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| 最大并发会话数 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* 从 1024 GB 到 4096 GB，增量为 256 GB

> [!IMPORTANT]
> 除以下区域外，其他所有区域的高级层目前均可提供超过 1 TB 的存储：中国东部、中国北部、德国中部、德国东北部、美国中西部、US DoD 区域和美国政府中部。 在这些区域，高级层中的最大存储限制为 1 TB。  有关详细信息，请参阅[P11-P15 当前限制](sql-database-single-database-scale.md#dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
> [!NOTE]
> 有关`tempdb`限制，请参阅[tempdb 限制](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)。

## <a name="next-steps"></a>后续步骤

- 有关单个数据库的 vCore 资源限制，请参阅[使用基于 vCore 的购买模型的单个数据库的资源限制](sql-database-vcore-resource-limits-single-databases.md)
- 有关弹性池的 vCore 资源限制，请参阅[使用基于 vCore 的购买模型的弹性池的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)
- 有关弹性池的 DTU 资源限制，请参阅[使用基于 DTU 的购买模型的弹性池的资源限制](sql-database-dtu-resource-limits-elastic-pools.md)
- 有关托管实例的资源限制，请参阅[托管实例资源限制](sql-database-managed-instance-resource-limits.md)。
- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-subscription-service-limits.md)。
- 有关数据库服务器上的资源限制的信息，请参阅 [SQL 数据库服务器资源限制概述](sql-database-resource-limits-database-server.md)了解有关服务器级别和订阅级别限制的信息。

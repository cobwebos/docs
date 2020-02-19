---
title: 缩放弹性池资源
description: 本页描述如何在 Azure SQL 数据库中缩放弹性池资源。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 3/14/2019
ms.openlocfilehash: daca108cfc8bb2e5b2a068170a4a0244c72c9592
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462592"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>在 Azure SQL 数据库中缩放弹性池资源

本文介绍如何在 Azure SQL 数据库中缩放适用于弹性池和共用数据库的计算和存储资源。

## <a name="change-compute-resources-vcores-or-dtus"></a>更改计算资源（Vcore 或 Dtu）

最初选取 Vcore 或 Edtu 的数目后，你可以根据使用[Azure 门户](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、 [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)、 [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)或[REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)的实际经验，动态缩放弹性池。

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>更改服务层或重新缩放计算大小的影响

更改弹性池的服务层或计算大小将遵循与单一数据库相似的模式，主要涉及执行以下步骤的服务：

1. 为弹性池创建新的计算实例  

    弹性池的新计算实例是使用请求的服务层和计算大小创建的。 对于某些服务层和计算大小更改的组合，必须在新的计算实例中创建每个数据库的副本，这涉及复制数据，并可能会对整体延迟造成影响。 无论如何，在此步骤中数据库仍保持联机状态，并且连接将继续定向到原始计算实例中的数据库。

2. 将连接的路由切换到新的计算实例

    与原始计算实例中的数据库的现有连接将被删除。 将为新的计算实例中的数据库建立任何新连接。 对于服务层和计算大小更改的某些组合，数据库文件在交换机中分离并重新附加。  无论如何，如果数据库不可用的时间通常不超过30秒，通常只需几秒钟，此开关就会导致短暂的服务中断。 如果在删除连接时运行了长时间运行的事务，则此步骤的持续时间可能需要更长的时间才能恢复中止的事务。 [加速数据库恢复](sql-database-accelerated-database-recovery.md)可以降低中止长时间运行事务的影响。

> [!IMPORTANT]
> 工作流中的任何步骤都不会丢失任何数据。

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>更改服务层或重新缩放计算大小的延迟

更改服务层或缩放单一数据库或弹性池的计算大小的估计延迟如下：

|服务层|基本单一数据库，</br>标准（S0-S1）|基本弹性池，</br>标准（S2-S12）， </br>超大规模 </br>常规用途单一数据库或弹性池|高级或业务关键单一数据库或弹性池|
|:---|:---|:---|:---|
|**基本单一数据库，</br> 标准（S0-S1）**|与所用空间无关 &bull; &nbsp;固定时间延迟</br>&bull; &nbsp;通常不到5分钟|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|
|**基本弹性池，</br>标准（S2-S12）、</br>超大规模 </br>常规用途单一数据库或弹性池**|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|与所用空间无关 &bull; &nbsp;固定时间延迟</br>&bull; &nbsp;通常不到5分钟|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|
|**高级或业务关键单一数据库或弹性池**|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|与数据复制所使用的数据库空间比例 &bull; &nbsp;滞后时间</br>&bull; 通常 &nbsp;使用的空间小于每 GB 的1分钟|

> [!NOTE]
>
> - 如果为弹性池更改服务层或重新缩放计算，则应使用池中所有数据库使用的空间的总和来计算估计值。
> - 如果将数据库移入或移出弹性池，则只有数据库使用的空间会影响延迟，而不会影响弹性池使用的空间。
>
> [!TIP]
> 若要监视正在进行的操作，请参阅：[使用 SQL REST API 管理操作](https://docs.microsoft.com/rest/api/sql/operations/list)、[使用 CLI 管理操作](/cli/azure/sql/db/op)、[使用 t-sql 监视操作](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)和以下两个 PowerShell 命令： [AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity)和[AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>更改服务层或重新缩放计算大小时的其他注意事项

- 弹性池的缩小 Vcore 或 Edtu 时，池所用空间必须小于目标服务层和池 Edtu 允许的最大大小。
- 当重新缩放弹性池的 eDTU 时，如果 (1) 目标池支持池的存储上限，(2) 存储上限超过了目标池附送的存储量，将产生额外存储费用。 例如，如果最大大小为 100 GB 的 100 eDTU 标准池缩小为 50 eDTU 标准池，那么将产生额外存储费用，因为目标池支持的最大大小为 100 GB，其附送的存储量仅为 50 GB。 因此，额外存储量为 100 GB – 50 GB = 50 GB。 有关额外存储定价的信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 如果实际使用的空间量小于附送的存储量，只要将数据库最大大小减少到附送的量，就能避免此项额外费用。

### <a name="billing-during-rescaling"></a>重新缩放时计费

将根据使用最高服务层级的数据库存在的每个小时 + 在该小时适用的计算大小进行计费，无论使用方式或数据库处于活动状态是否少于一小时。 例如，如果创建了单一数据库，五分钟后删除了它，则将按该数据库存在一小时收费。

## <a name="change-elastic-pool-storage-size"></a>更改弹性池存储大小

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

### <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型

- 可将存储预配到最大大小限制。

  - 对于“标准”或“常规用途”服务层级中的存储，按 10 GB 增量增减大小
  - 对于“高级”或“业务关键”服务层级中的存储，按 250 GB 增量增减大小
- 可以通过增大或减小其最大大小来预配弹性池的存储空间。
- 弹性池的存储价格等于存储量乘以服务层级的存储单价。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

### <a name="dtu-based-purchasing-model"></a>基于 DTU 的购买模型

- 弹性池的 eDTU 价格附送了一定容量的存储，无需额外费用。 超出附送的量后，可花费额外的费用预配额外的存储，但不能超过存储上限，不超过 1 TB 时，以 250 GB 为增量进行预配，超出 1 TB 时，以 256 GB 为增量进行预配。 有关附送存储量和大小上限，请参阅[弹性池：存储大小和计算大小](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)。
- 可通过 [Azure 门户](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)、[Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update) 为弹性池增加大小上限，以预配额外存储。
- 弹性池的额外存储价格等于额外存储量乘以服务层级的额外存储单价。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="next-steps"></a>后续步骤

有关总体资源限制，请参阅 [SQL 数据库基于 vCore 的资源限制 - 弹性池](sql-database-vcore-resource-limits-elastic-pools.md)和 [SQL 数据库基于 DTU 的资源限制 - 弹性池](sql-database-dtu-resource-limits-elastic-pools.md)。

---
title: 缩放弹性池资源 - Azure SQL 数据库 | Microsoft Docs
description: 本页描述如何在 Azure SQL 数据库中缩放弹性池资源。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 0f63739c8718ed7d6625bd18de4fdfff4df60276
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412332"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>在 Azure SQL 数据库中缩放弹性池资源

本文介绍如何在 Azure SQL 数据库中缩放适用于弹性池和已共用数据库的计算和存储资源。 

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>基于 vCore 的购买模型：更改弹性池存储大小

- 可将存储预配到最大大小限制。 
 - 对于标准存储，可以 10 GB 为增量增加或减少大小
 - 对于高级存储，可以 250 GB 为增量增加或减少大小
- 可以通过增大或减小其最大大小来预配弹性池的存储空间。
- 弹性池的存储价格等于存储量乘以服务层的存储单价。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>基于 vCore 的购买模型：更改弹性池计算资源 (vCore)

可按资源需求，通过 [Azure 门户](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 或 [REST API](/rest/api/sql/elasticpools/update) 提高或降低弹性池的性能级别。

- 重新缩放池 vCore 时，将暂时停止数据库连接。 此行为与重新缩放单一数据库（而非在池中）的 DTU 时的行为相同。 有关在重新缩放操作执行期间，停止数据库连接的持续时间和影响的详细信息，请参阅[重新缩放单一数据库的 DTU](#single-database-change-storage-size)。 
- 重新缩放池 vCore 的持续时间取决于池中所有数据库使用的总存储空间量。 一般而言，每 100 GB 重新缩放的平均延迟时间不超过 90 分钟。 例如，如果池中所有数据库使用的总空间为 200 GB，则重新缩放池的预计延迟时间将不超过 3 小时。 对标准层或基本层中的某些事例而言，重新缩放延迟时间可能不超过五分钟，不考虑所用空间量的影响。
- 一般而言，更改每个数据库的最小 vCore 或最大 vCore 的持续时间将不超过五分钟。
- 当缩小池 vCore 时，池所用空间必须小于目标服务层和池 vCore 所允许的最大大小。

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>基于 DTU 的购买模型：更改弹性池存储大小

- 弹性池的 eDTU 价格附送了一定容量的存储，无需额外费用。 超出附送的量后，可花费额外的费用预配额外的存储，但不能超过存储上限，不超过 1 TB 时，以 250 GB 为增量进行预配，超出 1 TB 时，以 256 GB 为增量进行预配。 有关附送存储量和大小上限，请参阅[弹性池：存储大小和性能级别](#elastic-pool-storage-sizes-and-performance-levels)。
- 可通过 [Azure 门户](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 或 [REST API](/rest/api/sql/elasticpools/update) 为弹性池增加大小上限，以预配额外存储。
- 弹性池的额外存储价格等于额外存储量乘以服务层的额外存储单价。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>基于 DTU 的购买模型：更改弹性池计算资源 (eDTU)

可按资源需求，通过 [Azure portal](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 或 [REST API](/rest/api/sql/elasticpools/update) 增加或减少弹性池可用的资源。

- 重新缩放池 eDTU 时，将暂时停止数据库连接。 此行为与重新缩放单一数据库（而非在池中）的 DTU 时的行为相同。 有关在重新缩放操作执行期间，停止数据库连接的持续时间和影响的详细信息，请参阅[重新缩放单一数据库的 DTU](#single-database-change-storage-size)。 
- 重新缩放池 eDTU 的持续时间取决于池中所有数据库使用的总存储空间量。 一般而言，每 100 GB 重新缩放的平均延迟时间不超过 90 分钟。 例如，如果池中所有数据库使用的总空间为 200 GB，则重新缩放池的预计延迟时间将不超过 3 小时。 对标准层或基本层中的某些事例而言，重新缩放延迟时间可能不超过五分钟，不考虑所用空间量的影响。
- 一般而言，更改每个数据库的最小 eDTU 或最大 eDTU 的持续时间将不超过五分钟。
- 当缩小池 eDTU 时，池所用空间必须小于目标服务层和池 eDTU 所允许的最大大小。
- 当重新缩放池 eDTU 时，如果 (1) 目标池支持池的存储上限，(2) 存储上限超过了目标池附送的存储量，将产生额外存储费用。 例如，如果最大大小为 100 GB 的 100 eDTU 标准池缩小为 50 eDTU 标准池，那么将产生额外存储费用，因为目标池支持的最大大小为 100 GB，其附送的存储量仅为 50 GB。 因此，额外存储量为 100 GB – 50 GB = 50 GB。 有关额外存储定价的信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 如果实际使用的空间量小于附送的存储量，只要将数据库最大大小减少到附送的量，就能避免此项额外费用。 

## <a name="next-steps"></a>后续步骤

有关总体资源限制，请参阅 [SQL 数据库基于 vCore 的资源限制 - 弹性池](sql-database-vcore-resource-limits-elastic-pools.md)和 [SQL 数据库基于 DTU 的资源限制 - 弹性池](sql-database-dtu-resource-limits-elastic-pools.md)。

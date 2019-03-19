---
title: 缩放弹性池资源 - Azure SQL 数据库 | Microsoft Docs
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
manager: craigg
ms.date: 3/14/2019
ms.openlocfilehash: d8aaf51c836a8e88c4e9b92798067167cd044e72
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58015370"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>在 Azure SQL 数据库中缩放弹性池资源

本文介绍如何在 Azure SQL 数据库中缩放适用于弹性池和已共用数据库的计算和存储资源。

## <a name="change-compute-resources-vcores-or-dtus"></a>更改计算资源 （Vcore 或 Dtu）

首先选择 Vcore 或 Edtu 数，你可以缩放弹性池向上或向下根据实际体验使用动态[Azure 门户](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)， [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)，则[Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)，或[REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)。

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>更改服务层或重新缩放计算实例大小的影响

更改服务层或计算的弹性池大小遵循类似的模式与单一数据库和主要涉及服务执行以下步骤：

1. 创建新弹性池的计算实例  

    请求的服务层和计算实例大小被创建新弹性池的计算实例。 对于服务层和计算大小更改的某些组合，每个数据库的副本必须这涉及到将数据复制到新的计算实例中创建，并且可以会对总体延迟。 无论如何，在此步骤中，数据库保持联机状态并连接继续指向原始的计算实例中的数据库。

2. 切换到新的计算实例的连接的路由

    将删除原始的计算实例中的数据库的现有连接。 在新的计算实例中对数据库建立任何新连接。 对于服务层和计算大小更改的某些组合，数据库文件分离再重新附加在切换期间。  无论如何，交换机可能会导致短暂的服务中断时数据库都不可用，通常为 30 秒内，通常只有几秒钟。 如果有长时间运行的运行时将删除连接的事务，此步骤的持续时间可能需要更长时间才能恢复中止的事务数。 [加速数据库恢复](sql-database-accelerated-database-recovery.md)可以减少从中止长时间运行的事务的影响。

> [!IMPORTANT]
> 在工作流中的任何步骤期间不会丢失任何数据。

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>更改服务层或重新缩放计算实例大小的滞后时间

若要更改服务层或重新缩放单一数据库或弹性池的计算大小的延迟是参数化，如下所示：

|服务层|基本单一数据库</br>标准 (S0-S1)|基本弹性池</br>标准 (S2-S12) </br>超大规模， </br>常规用途的单一数据库或弹性池|高级或业务关键的单个数据库或弹性池|
|:---|:---|:---|:---|
|**基本单一数据库</br>标准 (S0-S1)**|&bull; &nbsp;常量时间内延迟独立使用的空间</br>&bull; &nbsp;通常情况下，小于 5 分钟|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|
|**基本弹性池</br>标准 (S2-S12)</br>超大规模，</br>常规用途单一数据库或弹性池**|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|&bull; &nbsp;常量时间内延迟独立使用的空间</br>&bull; &nbsp;通常情况下，小于 5 分钟|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|
|**高级或业务关键的单个数据库或弹性池**|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|&bull; &nbsp;使用，因为如果数据复制的数据库空间成比例的延迟</br>&bull; &nbsp;通常情况下，小于 1 GB 的空间使用每分钟|

> [!NOTE]
>
> - 对于服务层更改或重新缩放弹性池的计算，应使用跨池中所有数据库使用的空间的总和来计算估计值。
> - 在移动数据库与弹性池的情况下仅使用数据库的空间会影响延迟，不使用弹性池的空间。
>
> [!TIP]
> 要监视进行中的操作，请参阅：[使用 SQL REST API 管理操作](https://docs.microsoft.com/rest/api/sql/operations/list)、[使用 CLI 管理操作](/cli/azure/sql/db/op)、[使用 T-SQL 监视操作](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)以及这两个 PowerShell 命令：[Get AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity)并[停止 AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>其他注意事项时更改服务层或重新缩放的计算大小

- 当缩小 Vcore 或弹性池的 Edtu，池所用空间必须小于允许的目标服务层和池 Edtu 大小最大值。
- 重新缩放 Vcore 或弹性池的 Edtu 时, 产生额外存储费用适用于目标池支持池 （1） 存储最大大小和 （2） 的存储最大大小超出了目标池附送的存储量。 例如，如果最大大小为 100 GB 的 100 eDTU 标准池缩小为 50 eDTU 标准池，那么将产生额外存储费用，因为目标池支持的最大大小为 100 GB，其附送的存储量仅为 50 GB。 因此，额外存储量为 100 GB – 50 GB = 50 GB。 有关额外存储定价的信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 如果实际使用的空间量小于附送的存储量，只要将数据库最大大小减少到附送的量，就能避免此项额外费用。

### <a name="billing-during-rescaling"></a>计费期间重新缩放

将根据使用最高服务层的数据库存在的每个小时 + 在该小时适用的计算大小进行计费，无论使用方式或数据库处于活动状态是否少于一小时。 例如，如果创建了单一数据库，五分钟后删除了它，则将按该数据库存在一小时收费。

## <a name="change-elastic-pool-storage-size"></a>更改弹性池存储大小

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

### <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型

- 可将存储预配到最大大小限制。

  - 对于标准或常规用途服务层中的存储，按 10 GB 增量增减大小
  - 对于高级或业务关键服务层中的存储，按 250 GB 增量增减大小
- 可以通过增大或减小其最大大小来预配弹性池的存储空间。
- 弹性池的存储价格等于存储量乘以服务层的存储单价。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

### <a name="dtu-based-purchasing-model"></a>基于 DTU 的购买模型

- 弹性池的 eDTU 价格附送了一定容量的存储，无需额外费用。 超出附送的量后，可花费额外的费用预配额外的存储，但不能超过存储上限，不超过 1 TB 时，以 250 GB 为增量进行预配，超出 1 TB 时，以 256 GB 为增量进行预配。 有关附送存储量和大小上限，请参阅[弹性池：存储大小和计算大小](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)。
- 可通过 [Azure 门户](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)、[Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update) 为弹性池增加大小上限，以预配额外存储。
- 弹性池的额外存储价格等于额外存储量乘以服务层的额外存储单价。 有关额外存储价格的详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="next-steps"></a>后续步骤

有关总体资源限制，请参阅 [SQL 数据库基于 vCore 的资源限制 - 弹性池](sql-database-vcore-resource-limits-elastic-pools.md)和 [SQL 数据库基于 DTU 的资源限制 - 弹性池](sql-database-dtu-resource-limits-elastic-pools.md)。

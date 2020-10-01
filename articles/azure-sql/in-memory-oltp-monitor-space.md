---
title: 监视 XTP 内存中存储
description: 估算和监视 XTP 内存中存储用量与容量；解决容量错误 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 2134cf1fda5f0f1699feb46582813d198304f92e
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91616374"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>监视 Azure SQL 数据库和 Azure SQL 托管实例中的内存中 OLTP 存储
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

使用[内存中 OLTP](in-memory-oltp-overview.md)时，内存优化表中的数据和表变量驻留在内存中 OLTP 存储内。

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>确定数据是否在内存中 OLTP 存储容量限制范围内

确定不同服务层级的存储上限。 每个“高级”和“业务关键”服务层级都具有最大内存中 OLTP 存储大小。

- [基于 DTU 的资源限制 - 单一数据库](database/resource-limits-dtu-single-databases.md)
- [基于 DTU 的资源限制 - 弹性池](database/resource-limits-dtu-elastic-pools.md)
- [基于 vCore 的资源限制 - 单一数据库](database/resource-limits-vcore-single-databases.md)
- [基于 vCore 的资源限制 - 弹性池](database/resource-limits-vcore-elastic-pools.md)
- [基于 vCore 的资源限制 - 托管实例](managed-instance/resource-limits.md)

估计内存优化表的内存要求，如同在 Azure SQL 数据库和 Azure SQL 托管实例中估计 SQL Server 的内存要求一样。 需要几分钟时间来查看[估计内存要求](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables)。

表和表变量行以及索引都将计入最大用户数据大小。 此外，ALTER TABLE 需要足够的空间来创建新版的完整表及其索引。

超过此限制后，在 Azure SQL 数据库的单个数据库和 Azure SQL 托管实例的数据库中，插入和更新操作可能会开始失败，出现错误 41823，在 Azure SQL 数据库的弹性池中也会发生相应的操作失败，并出现错误 41840。 到时，需要删除数据以回收内存，或升级数据库的服务层级或计算大小。

## <a name="monitoring-and-alerting"></a>监视和警报

可以在 [Azure 门户](https://portal.azure.com/)中，通过计算大小的存储上限百分比来监视内存中存储用量：

1. 在“数据库”边栏选项卡上，找出“资源使用率”框并单击“编辑”。
2. 选择指标 `In-Memory OLTP Storage percentage`。
3. 如果要添加警报，请单击“资源使用率”框以打开“度量值”边栏选项卡，并单击“添加警报”。

或者，使用以下查询来显示内存中存储的使用率：

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>更正超过内存中 OLTP 存储的情况 - 错误 41823 和 41840

数据库达到内存中 OLTP 存储上限会导致 INSERT、UPDATE、ALTER 和 CREATE 操作失败，出现错误消息 41823（针对单一数据库）或错误 41840（针对弹性池）。 这两个错误均会导致活动的事务中止。

错误消息 41823 和 41840 指示数据库或池中的内存优化表和表变量已达到最大的内存中 OLTP 存储大小。

若要解决此错误，请执行以下操作之一：

- 从内存优化表中删除数据，为此，可以将数据卸载到传统的基于磁盘的表；或者，
- 将服务层级升级到具有足够内存中存储的服务层级，使保存需要保留在内存优化表中的数据。

> [!NOTE]
> 在极少数情况下，错误 41823 和 41840 可能是暂时的，即有足够的可用内存中 OLTP 存储，重试后该操作成功。 因此，我们建议既要监视总体的可用内存中 OLTP 存储，又要在首次遇到错误 41823 或 41840 时重试。 有关重试逻辑的详细信息，请参阅[内存中 OLTP 的冲突检测和重试逻辑](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic)。

## <a name="next-steps"></a>后续步骤

有关监视指南，请参阅[使用动态管理视图进行监视](database/monitoring-with-dmvs.md)。

---
title: 从 DTU 迁移到 vCore
description: 从 DTU 模型迁移到 vCore 模型。 迁移到 vCore 类似于在标准层和高级层之间进行升级或降级。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945404"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>从基于 DTU 的模型迁移到基于 vCore 的模型

## <a name="migrate-a-database"></a>迁移数据库

将数据库从基于 DTU 的购买模型迁移到基于 vCore 的购买模型类似于在基于 DTU 的购买模型中的标准和高级服务层之间进行升级或降级。

## <a name="migrate-databases-that-use-geo-replication"></a>迁移使用异地复制的数据库

从基于 DTU 的模型迁移到基于 vCore 的购买模型类似于升级或降级标准和高级服务层中的数据库之间的异地复制关系。 在迁移过程中，无需停止异地复制，但必须遵循以下顺序规则：

- 升级时，必须先升级辅助数据库，再升级主数据库。
- 降级时，必须反转顺序：先降级主数据库，再降级辅助数据库。

在两个弹性池之间使用异地复制时，我们建议你将一个池指定为主池，将另一个池指定为辅助池。 在这种情况下，当你迁移弹性池时，应使用相同的顺序指南。 但是，如果具有包含主数据库和辅助数据库的弹性池，请将利用率较高的池视为主数据库，并按照相应的顺序规则进行排序。  

下表提供了针对特定迁移方案的指南：

|当前服务层级|目标服务层级|迁移类型|用户操作|
|---|---|---|---|
|Standard|常规用途|横向|可按任意顺序迁移，但需确保 vCore 大小适当*|
|Premium|业务关键|横向|可按任意顺序迁移，但需确保 vCore 大小适当*|
|Standard|业务关键|升级|必须先迁移辅助数据库|
|业务关键|Standard|降级|必须先迁移主数据库|
|Premium|常规用途|降级|必须先迁移主数据库|
|常规用途|Premium|升级|必须先迁移辅助数据库|
|业务关键|常规用途|降级|必须先迁移主数据库|
|常规用途|业务关键|升级|必须先迁移辅助数据库|
||||

\* 作为经验法则，标准层中每100个 Dtu 至少需要1个 vCore，高级层中的每个 125 Dtu 至少需要 1 vCore。 有关详细信息，请参阅[基于 vCore 的购买模型](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model)。

## <a name="migrate-failover-groups"></a>迁移故障转移组

迁移包含多个数据库的故障转移组需要单独迁移主数据库和辅助数据库。 在此过程中，请遵循相同的注意事项和顺序规则。 将数据库转换为基于 vCore 的购买模型后，故障转移组将仍然有效，并具有相同的策略设置。

### <a name="create-a-geo-replication-secondary-database"></a>创建异地复制辅助数据库

仅可通过使用与主数据库相同的服务层来创建异地复制辅助数据库（异地辅助数据库）。 对于日志生成速率较高的数据库，我们建议使用与主数据库相同的计算大小创建异地辅助数据库。

如果要在弹性池中为单个主数据库创建异地辅助数据库，请确保该池的 `maxVCore` 设置与主数据库的计算大小匹配。 如果要为另一个弹性池的主数据库创建异地辅助数据库，我们建议使用相同的 `maxVCore` 设置。

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>使用数据库复制将基于 DTU 的数据库转换为基于 vCore 的数据库

可将采用基于 DTU 的计算大小的任何数据库复制到采用基于 vCore 的计算大小的数据库，且无需遵守上述限制或特殊的顺序，前提是目标计算大小支持源数据库的最大数据库大小。 数据库复制将创建复制操作开始时的数据快照，并且不会在源和目标之间同步数据。

## <a name="next-steps"></a>后续步骤

- 有关适用于单一数据库的特定计算大小和存储大小选项，请参阅[针对单一数据库的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 有关弹性池可用的特定计算大小和存储大小选项，请参阅适用于[弹性池的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)。

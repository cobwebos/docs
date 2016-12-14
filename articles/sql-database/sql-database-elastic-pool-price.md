---
title: "SQL 数据库弹性池价格和性能"
description: "特定于弹性数据库池的定价信息。"
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: e8d64ce5-e30a-445d-8c18-35b3825c6e76
ms.service: sql-database
ms.custom: sharded databases pool
ms.devlang: NA
ms.date: 05/27/2016
ms.author: srinia
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4e4afe2a4f5f3a06696ec0044368f272708667a0


---
# <a name="elastic-database-pool-billing-and-pricing-information"></a>弹性数据库池计费和定价信息
弹性数据库池按以下情况计费：

* 弹性池一创建即计费，即使池中没有数据库。
* 弹性池按小时计费。 该计量频率与单一数据库性能级别的计量频率相同。
* 如果将弹性池的大小调整为新的 eDTU 量，则在调整操作完成之前，不会按新的 eDTU 量计费。 这种计费所遵循的模式与更改独立数据库的性能级别所遵循的模式相同。
* 弹性池的价格取决于池的 eDTU 数。 弹性池的价格与池内弹性数据库的数目和使用率无关。
* 价格的计算公式为：（池 eDTU 的数量）x（每 eDTU 的单位价格）。

弹性池的 eDTU 单价高于同一服务层中独立数据库的 DTU 单价。 有关详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 

若要了解 eDTU 和服务层，请参阅 [SQL 数据库选项和性能](sql-database-service-tiers.md)。

## <a name="next-steps"></a>后续步骤
* [创建弹性数据库池](sql-database-elastic-pool-create-portal.md)
* [监视、管理弹性数据库池并调整其大小](sql-database-elastic-pool-manage-portal.md)
* [SQL 数据库选项和性能：了解每个服务层提供的功能](sql-database-service-tiers.md)
* [用于识别适用于弹性数据库池的数据库的 PowerShell 脚本](sql-database-elastic-pool-database-assessment-powershell.md)




<!--HONumber=Nov16_HO3-->



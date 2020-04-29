---
title: 术语词汇表
description: Azure SQL 数据库术语表
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2020
ms.openlocfilehash: 733901d38703e02ab7dbe811b0f80a1dfedf03d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76705779"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL 数据库术语表

|上下文|术语|详细信息|
|:---|:---|:---|
|Azure 服务|Azure SQL 数据库或 SQL 数据库|[Azure SQL 数据库服务](sql-database-technical-overview.md)|
|购买模型|基于 DTU 的购买模型|[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)|
||基于 vCore 的购买模型|[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)|
|部署选项 |单一数据库|[单一数据库](sql-database-single-database.md)|
||弹性池|[弹性池](sql-database-elastic-pool.md)|
||托管实例|[托管实例](sql-database-managed-instance.md)|
|服务层|基本、标准、高级、常规用途、超大规模、业务关键|有关 vCore 模型中的服务层级，请参阅[单一数据库和弹性池](sql-database-service-tiers-vcore.md#service-tiers)以及[托管实例](sql-database-managed-instance.md#managed-instance-service-tiers)。 有关 DTU 模型中的服务层级，请参阅 [DTU 模型](sql-database-service-tiers-dtu.md#compare-the-dtu-based-service-tiers)。|
|计算层|无服务器计算|[无服务器计算](sql-database-service-tiers-vcore.md#compute-tiers)
||预配计算|[预配计算](sql-database-service-tiers-vcore.md#compute-tiers)
|计算的代|Gen5、M 系列、Fsv2 系列|[硬件代系](sql-database-service-tiers-vcore.md#hardware-generations)
|服务器实体|SQL 数据库服务器或数据库服务器|[数据库服务器](sql-database-servers.md)|
||SQL 数据库托管实例服务器、托管实例服务器或实例服务器|[托管实例](sql-database-managed-instance.md)|
|资源类型|vCore|提供给单一数据库、弹性池或托管实例的计算资源的 CPU 核心。|
||计算大小和存储量|计算大小是可用于单一数据库、弹性池或托管实例的最大 CPU、内存和其他非存储相关资源量。  存储大小是可用于单一数据库、弹性池或托管实例的最大存储量。  有关 vCore 模型中的大小选项，请参阅 [vCore 单一数据库](sql-database-vcore-resource-limits-single-databases.md)、[vCore 弹性池](sql-database-vcore-resource-limits-elastic-pools.md)和[托管实例](sql-database-managed-instance-resource-limits.md)。  有关 DTU 模型中的大小选项，请参阅 [DTU 单一数据库](sql-database-dtu-resource-limits-single-databases.md)和 [DTU 弹性池](sql-database-dtu-resource-limits-elastic-pools.md)。

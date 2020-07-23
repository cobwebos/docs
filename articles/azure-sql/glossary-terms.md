---
title: 术语词汇表
titleSuffix: Azure SQL
description: 用于在 Azure VM 上使用 Azure SQL 数据库、Azure SQL 托管实例和 SQL 的术语术语表。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2020
ms.openlocfilehash: f05871c41e2deec3f6a52446844c0b8fd2c9038d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220801"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL 数据库术语表
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>Azure SQL Database

|上下文|术语|详细信息|
|:---|:---|:---|
|Azure 服务|Azure SQL 数据库或 SQL 数据库|[Azure SQL 数据库](database/sql-database-paas-overview.md)|
|购买模型|基于 DTU 的购买模型|[基于 DTU 的购买模型](database/service-tiers-dtu.md)|
||基于 vCore 的购买模型|[基于 vCore 的购买模型](database/service-tiers-vcore.md)|
|部署选项 |单一数据库|[单一数据库](database/single-database-overview.md)|
||弹性池|[弹性池](database/elastic-pool-overview.md)|
|服务层|基本、标准、高级、常规用途、超大规模、业务关键|对于 vCore 模型中的服务层，请参阅[SQL 数据库服务层](database/service-tiers-vcore.md#service-tiers)。 有关 DTU 模型中的服务层级，请参阅 [DTU 模型](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers)。|
|计算层|无服务器计算|[无服务器计算](database/service-tiers-vcore.md#compute-tiers)
||预配计算|[预配计算](database/service-tiers-vcore.md#compute-tiers)
|计算的代|Gen5、M 系列、Fsv2 系列|[硬件代系](database/service-tiers-vcore.md#hardware-generations)
|服务器实体| 服务器 |[逻辑 SQL 服务器](database/logical-servers.md)|
|资源类型|vCore|提供给单一数据库（弹性池）的计算资源的 CPU 核心。 |
||计算大小和存储量|计算大小是适用于单个数据库的最大 CPU、内存和其他非存储相关资源的最大数量，也可以是弹性池。  存储大小是单个数据库或弹性池可用的最大存储量。 有关 vcore 模型中的调整大小选项，请参阅[vcore 单数据库](database/resource-limits-vcore-single-databases.md)和[vcore 弹性池](database/resource-limits-vcore-elastic-pools.md)。  (../managed-instance/resource-limits.md).  有关 DTU 模型中的大小选项，请参阅 [DTU 单一数据库](database/resource-limits-dtu-single-databases.md)和 [DTU 弹性池](database/resource-limits-dtu-elastic-pools.md)。

## <a name="azure-sql-managed-instance"></a>Azure SQL 托管实例

|上下文|术语|详细信息|
|:---|:---|:---|
|Azure 服务|Azure SQL 托管实例|[SQL 托管实例](managed-instance/sql-managed-instance-paas-overview.md)|
|购买模型|基于 vCore 的购买模型|[基于 vCore 的购买模型](database/service-tiers-vcore.md)|
|部署选项 |单实例|[单个实例](managed-instance/sql-managed-instance-paas-overview.md)|
||实例池（预览）|[实例池](managed-instance/instance-pools-overview.md)|
|服务层|常规用途，业务关键|[SQL 托管实例服务层](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|计算层|预配计算|[预配计算](database/service-tiers-vcore.md#compute-tiers)|
|计算的代|Gen5|[硬件代系](database/service-tiers-vcore.md#hardware-generations)
|服务器实体|托管实例或实例| 不适用，因为 SQL 托管实例本身就是服务器 |
|资源类型|vCore|提供给 SQL 托管实例计算资源的 CPU 核心。|
||计算大小和存储量|计算大小是适用于 SQL 托管实例的 CPU、内存和其他非存储相关资源的最大数量。  存储大小是适用于 SQL 托管实例的最大存储量。  对于大小选项，则为[SQL 托管实例](managed-instance/resource-limits.md)。 |

## <a name="sql-on-azure-vm"></a>Azure VM 上的 SQL

此处需要更多内容

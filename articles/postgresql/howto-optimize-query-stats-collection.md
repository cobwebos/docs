---
title: 在 Azure Database for PostgreSQL 服务器查询统计信息集合中优化查询统计信息集合
description: 本文介绍了如何在 Azure Database for PostgreSQL 服务器上优化查询统计信息集合。
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 076442d85d7f628504cca95c36f3e99f4d0c5117
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966681"
---
# <a name="optimize-query-statistics-collection-in-azure-database-for-postgresql-server"></a>在 Azure Database for PostgreSQL 服务器中优化查询统计信息集合 
本文介绍了如何在 Azure Database for PostgreSQL 服务器中优化查询统计信息集合。

## <a name="using-pgstatsstatements"></a>使用 pg_stats_statements
**Pg_stat_statements** 是 Azure Database for PostgreSQL 中默认启用的一个 PostgreSQL 扩展。 此扩展提供了一种方法来跟踪服务器执行的所有 SQL 语句的执行统计信息。 但是，此模块会挂接到每个查询执行，并且性能成本较高。 启用 **pg_stat_statements** 会强制将查询文本写入到磁盘上的文件。

对于具有包含长查询文本的唯一查询的客户或者未主动监视 **pg_stat_statements** 的客户，建议通过设置 `pg_stat_statements.track = NONE` 来禁用 **pg_stat_statements** 以实现最佳性能。

对于某些客户工作负荷，我们看到禁用 **pg_stat_statements** 可以实现高达 50% 的性能提升。 但是，禁用 pg_stat_statements 的代价是无法对性能问题进行故障排除。

若要设置 `pg_stat_statements.track = NONE`，请执行以下操作：

- 在 Azure 门户中，导航到 [PostgreSQL 资源管理页面并选择服务器参数边栏选项卡](howto-configure-server-parameters-using-portal.md)。

![PostgreSQL 服务器参数边栏选项卡](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- 使用 [Azure CLI](howto-configure-server-parameters-using-cli.md)：az postgres server configuration set `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`。

## <a name="using-query-store"></a>使用“查询存储” 
Azure Database for PostgreSQL 中的[查询存储](concepts-query-store.md)功能提供了性能更高的方法来跟踪查询统计信息，建议使用它来替代 *pg_stats_statements*。 

## <a name="next-steps"></a>后续步骤
考虑在 [Azure 门户](howto-configure-server-parameters-using-portal.md)中或使用 [Azure CLI](howto-configure-server-parameters-using-cli.md) 设置 `pg_stat_statements.track = NONE`。

有关详细信息，请参阅[“查询存储”使用方案](concepts-query-store-scenarios.md)和[“查询存储”最佳做法](concepts-query-store-best-practices.md)。 

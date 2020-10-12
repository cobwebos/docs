---
title: 应用开发最佳做法 - Azure Database for MySQL
description: 了解使用 Azure Database for MySQL 构建应用的最佳做法。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: dc9764ce68d54418578c293833c1fd38080ba0ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91538902"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>使用 Azure Database for MySQL 构建应用程序的最佳做法 

下面是一些可帮助你使用 Azure Database for MySQL 构建云就绪应用程序的最佳做法。 这些最佳做法可以减少应用开发时间。 

## <a name="configuration-of-application-and-database-resources"></a>应用程序和数据库资源的配置

### <a name="keep-the-application-and-database-in-the-same-region"></a>使应用程序和数据库位于同一区域中
在 Azure 中部署应用程序时，请确保所有依赖项都位于同一区域中。 跨区域或可用性区域分布实例会造成网络延迟，这可能会影响应用程序的总体性能。 

### <a name="keep-your-mysql-server-secure"></a>确保 MySQL 服务器的安全
将 MySQL 服务器配置为[安全的](https://docs.microsoft.com/azure/mysql/concepts-security)不能公开访问的服务器。 使用以下选项之一来保护服务器： 
- [防火墙规则](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)
- [虚拟网络](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) 
- [Azure 专用链接](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

为了安全起见，必须始终通过 SSL 连接到 MySQL 服务器，并将 MySQL 服务器和应用程序配置为使用 TLS 1.2。 了解[如何配置 SSL/TLS](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security)。 

### <a name="tune-your-server-parameters"></a>优化服务器参数
对于读取密集型工作负荷优化服务器参数，`tmp_table_size` 和 `max_heap_table_size` 有助于优化性能。 若要计算这些变量所需的值，请查看每连接内存值总计和基本内存。 每连接内存参数（不包括 `tmp_table_size`）的总和与基本内存一起构成了服务器的总内存。

若要计算 `tmp_table_size` 和 `max_heap_table_size` 的最大可能大小，请使用以下公式：

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> 总内存指的是服务器在预配的 Vcore 中具有的内存总量。  例如，在常规用途的双 vCore Azure Database for MySQL 服务器中，总内存为 5 GB * 2。 可以在[定价层](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)文档中找到有关每个层的内存的更多详细信息。
>
> 基本内存指的是 MySQL 在服务器启动时将初始化和分配的内存变量，例如 `query_cache_size` 和 `innodb_buffer_pool_size`。 每连接内存（例如 `sort_buffer_size` 和 `join_buffer_size`）是仅当查询需要时才分配的内存。

### <a name="create-non-admin-users"></a>创建非管理员用户 
为每个数据库[创建非管理员用户](https://docs.microsoft.com/azure/mysql/howto-create-users)。 通常，用户名被标识为数据库名称。

### <a name="reset-your-password"></a>重置密码
可以使用 Azure 门户为 MySQL 服务器[重置密码](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password)。 

重置生产数据库的服务器密码可能会导致应用程序关闭。 对于任何生产工作负荷，最好是在非高峰时间为其重置密码，将其对应用程序用户的影响降到最低。

## <a name="performance-and-resiliency"></a>性能和复原能力 
下面是一些可以用来帮助调试应用程序性能问题的工具和做法。

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>启用慢查询日志来查明性能问题
可以在服务器上启用[慢查询日志](https://docs.microsoft.com/azure/mysql/concepts-server-logs)和[审核日志](https://docs.microsoft.com/azure/mysql/concepts-audit-logs)。 对慢查询日志进行分析有助于查明性能瓶颈以进行故障排除。 

还可通过 Azure Monitor 日志、Azure 事件中心和存储帐户中的 Azure 诊断日志获得审核日志。 请参阅[如何排查查询性能问题](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)。

### <a name="use-connection-pooling"></a>使用连接池
管理数据库连接可能会对整个应用程序的性能造成很大的影响。 若要优化性能，必须减少建立连接的次数，以及在关键代码路径中建立连接的时间。 使用[连接池](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended)连接到 Azure Database for MySQL 以提高复原能力和性能。 

你可以使用 [ProxySQL](https://proxysql.com/) 连接池程序来有效地管理连接。 使用连接池程序可以减少空闲连接并重用现有连接，这有助于避免问题。 有关详细信息，请参阅[如何设置 ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842)。 

### <a name="retry-logic-to-handle-transient-errors"></a>用来处理暂时性错误的重试逻辑
你的应用程序可能会遇到[暂时性错误](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors)：到数据库的连接间歇性断开或丢失。 在这种情况下，服务器在 5 到 10 秒内重试一两次后就会启动并运行。 

良好的做法是在第一次重试前等待 5 秒。 然后，每次重试都逐步延长等待时间，最多 60 秒。 限制最大重试次数。达到该次数时，应用程序会认为操作失败，随后你就可以进一步进行调查。 有关详细信息，请参阅[如何排查连接错误](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues)。 

### <a name="enable-read-replication-to-mitigate-failovers"></a>启用读取复制以缓解故障转移问题
对于故障转移场景，可以使用[数据传入复制](https://docs.microsoft.com/azure/mysql/howto-data-in-replication)。 使用读取副本时，不会发生源服务器和副本服务器之间的自动故障转移。 

你会注意到源和副本之间的滞后，因为复制是异步的。 网络延迟可能会受到许多因素的影响，如在源服务器上运行的工作负荷的大小以及数据中心之间的延迟。 大多数情况下，副本延迟在几秒钟到几分钟之间。

## <a name="database-deployment"></a>数据库部署 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>在 CI/CD 部署管道中配置 Azure Database for MySQL 任务
有时，你需要将更改部署到数据库。 在这种情况下，可以通过 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) 使用持续集成 (CI) 和持续交付 (CD)，并使用针对 [MySQL 服务器](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops)的任务通过对数据库运行自定义脚本来更新数据库。

### <a name="use-an-effective-process-for-manual-database-deployment"></a>使用有效的过程手动部署数据库 
在手动部署数据库的过程中，请执行以下步骤以最大程度地减少停机时间或降低部署失败的风险： 

1. 使用 [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) 或 [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html)，在新数据库上创建生产数据库的副本。 
2. 使用数据库所需的新架构更改或更新来更新新数据库。 
3. 将生产数据库置于只读状态。 在部署完成之前，不应当对生产数据库执行写入操作。 
4. 使用步骤 1 中新更新的数据库测试你的应用程序。
5. 部署你的应用程序更改，并确保应用程序目前正在使用具有最新更新的新数据库。 
6. 保留旧的生产数据库，以便回滚更改。 然后，你可以评估是删除旧的生产数据库，还是根据需要将其导出到 Azure 存储。 

>[!NOTE]
>如果应用程序类似于电子商务应用，并且不能将其置于只读状态，请在进行备份后直接在生产数据库上部署更改。 这些更改应在非高峰时间（此时发往应用的流量较小）进行，以最大程度地降低影响，因为某些用户可能会遇到请求失败的情况。 
>
>请确保应用程序代码还处理所有失败的请求。

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>使用 MySQL 原生指标来查看工作负荷是否超出内存中临时表的大小
使用读取密集型工作负荷时，针对 MySQL 服务器运行的查询可能会超出内存中临时表的大小。 读取密集型工作负荷可能会导致服务器改将临时表写入到磁盘，这会影响应用程序的性能。 若要确定服务器是否由于超出临时表大小而写入到磁盘，请查看以下指标：

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
`created_tmp_disk_tables` 指标指明在磁盘上创建了多少表。 `created_tmp_table` 指标告诉你必须在内存中生成多少临时表，具体取决于你的工作负荷。 若要确定运行特定查询是否会使用临时表，请对查询运行 [EXPLAIN](https://dev.mysql.com/doc/refman/8.0/en/explain.html) 语句。 如果查询会使用临时表运行，则 `extra` 列中的详细信息会指示 `Using temporary`。

若要计算查询溢出到磁盘的工作负荷所占百分比，请在以下公式中使用指标值：

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

理想情况下，此百分比应小于 25%。 如果看到百分比为 25% 或更大，则建议修改两个服务器参数：tmp_table_size 和 max_heap_table_size。

## <a name="database-schema-and-queries"></a>数据库架构和查询

下面是构建数据库架构和查询时需要注意的一些技巧。

### <a name="use-the-right-datatype-for-your-table-columns"></a>为表列使用正确的数据类型
根据要存储的数据的类型使用正确的数据类型可以优化存储并减少由于数据类型不正确而可能发生的错误。

### <a name="use-indexes"></a>使用索引
若要避免慢速查询，可以使用索引。 索引有助于快速查找具有特定列的行。 请参阅[如何在 MySQL 中使用索引](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html)。

### <a name="use-explain-for-your-select-queries"></a>对你的 SELECT 查询使用 EXPLAIN
使用 `EXPLAIN` 语句可以深入了解 MySQL 正在执行什么操作来运行查询。 它可以帮助你检测查询中的瓶颈或问题。 请参阅[如何使用 EXPLAIN 分析查询性能](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)。



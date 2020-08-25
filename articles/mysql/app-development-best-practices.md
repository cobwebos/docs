---
title: 应用开发最佳做法-Azure Database for MySQL
description: 了解使用 Azure Database for MySQL 生成应用的最佳做法。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 93bd6972a89065832a20fbd66949cde5b7510534
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794202"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-mysql"></a>使用 Azure Database for MySQL 生成应用程序的最佳做法 

下面是一些最佳做法，可帮助你使用 Azure Database for MySQL 生成云就绪应用程序。 这些最佳做法可以减少应用的开发时间。 

## <a name="configuration-of-application-and-database-resources"></a>配置应用程序和数据库资源

### <a name="keep-the-application-and-database-in-the-same-region"></a>使应用程序和数据库位于同一区域
在 Azure 中部署应用程序时，请确保所有依赖项都位于同一区域。 跨区域或可用性区域分配实例会产生网络延迟，这可能会影响应用程序的总体性能。 

### <a name="keep-your-mysql-server-secure"></a>保证 MySQL 服务器的安全
将 MySQL 服务器配置为 [安全](https://docs.microsoft.com/azure/mysql/concepts-security) 且无法公开访问。 使用以下选项之一来保护服务器： 
- [防火墙规则](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)
- [虚拟网络](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) 
- [Azure 专用链接](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

为安全，必须始终通过 SSL 连接到 MySQL 服务器，并将 MySQL server 和应用程序配置为使用 TLS 1.2。 请参阅 [如何配置 SSL/TLS](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security)。 

### <a name="tune-your-server-parameters"></a>优化服务器参数
对于读取繁重的工作负荷，优化服务器参数， `tmp_table_size` `max_heap_table_size` 有助于优化性能。 若要计算这些变量所需的值，请查看每个连接的总内存值和基本内存。 每连接内存参数的总和，其中不包括 `tmp_table_size` 服务器的总内存的基本内存帐户。

若要计算和的可能的最大大小 `tmp_table_size` `max_heap_table_size` ，请使用以下公式：

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Total memory 指示服务器在预配的 Vcore 中具有的内存总量。  例如，在常规用途 vCore Azure Database for MySQL 服务器中，总内存为 5 GB * 2。 有关 [定价层](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) 文档中每个层的内存的详细信息，请参阅。
>
> 基本内存指示在 `query_cache_size` `innodb_buffer_pool_size` 服务器启动时将初始化和分配的内存变量，如和。 每连接内存（如 `sort_buffer_size` 和 `join_buffer_size` ）是仅当查询需要时分配的内存。

### <a name="create-non-admin-users"></a>创建非管理员用户 
为每个数据库[创建非管理员用户](https://docs.microsoft.com/azure/mysql/howto-create-users)。 通常，用户名被标识为数据库名称。

### <a name="reset-your-password"></a>重置密码
可以使用 Azure 门户为 MySQL 服务器 [重置密码](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) 。 

重置生产数据库的服务器密码可能会导致应用程序关闭。 最好在非高峰时间为任何生产工作负荷重置密码，以将对应用程序用户的影响降到最低。

## <a name="performance-and-resiliency"></a>性能和复原 
下面是一些可以用来帮助调试应用程序性能问题的工具和做法。

### <a name="enable-slow-query-logs-to-identify-performance-issues"></a>启用慢查询日志来确定性能问题
你可以在服务器上启用 [慢查询日志](https://docs.microsoft.com/azure/mysql/concepts-server-logs) 和 [审核日志](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) 。 分析慢查询日志有助于识别故障排除的性能瓶颈。 

还可以通过 Azure Monitor 日志、Azure 事件中心和存储帐户中 Azure 诊断日志使用审核日志。 请参阅 [如何排查查询性能问题](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)。

### <a name="use-connection-pooling"></a>使用连接池
管理数据库连接可能会对整个应用程序的性能造成很大的影响。 若要优化性能，必须减少建立连接的次数，以及在关键代码路径中建立连接的时间。 使用 [连接池](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) 连接到 Azure Database for MySQL 以提高复原能力和性能。 

可以使用 [ProxySQL](https://proxysql.com/) 连接池来有效地管理连接。 使用连接池池可能会降低空闲连接并重复使用现有连接，这有助于避免出现问题。 请参阅 [如何设置 ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) 以了解详细信息。 

### <a name="retry-logic-to-handle-transient-errors"></a>用于处理暂时性错误的重试逻辑
您的应用程序可能会遇到 [暂时性错误](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) ，在这种情况下，到数据库的连接会间歇删除或丢失。 在这种情况下，服务器在5到10秒内重试一次后就会启动并运行。 

最佳做法是在第一次重试前等待5秒。 然后，每次重试都按增量递增等待，最多60秒。 限制应用程序认为操作失败的最大重试次数，以便进一步调查。 有关详细信息，请参阅 [如何对连接错误进行故障排除](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) 。 

### <a name="enable-read-replication-to-mitigate-failovers"></a>启用读取复制以缓解故障转移
可以将 [数据传入复制](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) 用于故障转移方案。 使用读取副本时，不会发生主服务器和副本服务器之间的自动故障转移。 

由于复制是异步的，你会注意到主副本和副本之间的延迟。 网络延迟可能会受到许多因素的影响，例如，在主服务器上运行的工作负荷的大小以及数据中心之间的延迟。 在大多数情况下，副本延迟范围从几秒钟到几分钟。

## <a name="database-deployment"></a>数据库部署 

### <a name="configure-an-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>在 CI/CD 部署管道中配置 Azure database for MySQL 任务
偶尔，你需要将更改部署到数据库。 在这种情况下，你可以通过 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) 使用持续集成 (CI) 和持续交付 (CD) ，并使用 [MySQL 服务器](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) 的任务通过对其运行自定义脚本来更新数据库。

### <a name="use-an-effective-process-for-manual-database-deployment"></a>使用有效的过程手动部署数据库 
在手动数据库部署过程中，请执行以下步骤以最大程度地减少停机时间或降低部署失败的风险： 

1. 使用 [mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) 或 [MySQL 工作台](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html)在新数据库上创建生产数据库的副本。 
2. 将新数据库更新为数据库所需的新架构更改或更新。 
3. 使生产数据库处于只读状态。 在完成部署之前，不应在生产数据库上进行写操作。 
4. 在步骤1中用新更新的数据库测试应用程序。
5. 部署应用程序更改，并确保应用程序现在正在使用具有最新更新的新数据库。 
6. 保留旧的生产数据库，以便回滚更改。 然后，你可以评估以删除旧的生产数据库，或在需要时将其导出到 Azure 存储。 

>[!NOTE]
>如果应用程序类似于电子商务应用程序，并且不能将其置于只读状态，请在进行备份后直接在生产数据库上部署更改。 这些更改应在非高峰时间发生，并向应用程序发送低流量，以最大程度地降低影响，因为某些用户可能会遇到失败的请求。 
>
>请确保应用程序代码还处理所有失败的请求。

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>使用 MySQL 本机指标来查看工作负荷是否超出内存中临时表大小
使用读取密集型工作负荷时，针对 MySQL 服务器运行的查询可能会超出内存中临时表的大小。 读取繁重的工作负荷可能会导致服务器切换到将临时表写入磁盘，这会影响应用程序的性能。 若要确定服务器是否由于超过临时表大小而写入磁盘，请查看以下指标：

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
`created_tmp_disk_tables`指标表示在磁盘上创建的表的数量。 `created_tmp_table`指标会告诉您工作负荷需要在内存中形成多少临时表。 若要确定运行特定查询是否将使用临时表，请对查询运行 [说明](https://dev.mysql.com/doc/refman/8.0/en/explain.html) 语句。 列中的详细信息 `extra` 指示 `Using temporary` 查询是否将使用临时表运行。

若要使用溢出到磁盘的查询来计算工作负荷的百分比，请在以下公式中使用指标值：

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

理想情况下，此百分比应小于25%。 如果看到百分比为25% 或更大，则建议修改两个服务器参数，tmp_table_size 和 max_heap_table_size。

## <a name="database-schema-and-queries"></a>数据库架构和查询

下面是生成数据库架构和查询时需要注意的一些技巧。

### <a name="use-the-right-datatype-for-your-table-columns"></a>对表列使用正确的数据类型
根据要存储的数据类型使用正确的数据类型可以优化存储并减少由于数据类型不正确而导致的错误。

### <a name="use-indexes"></a>使用索引
若要避免速度慢的查询，可使用索引。 索引可以帮助快速查找具有特定列的行。 请参阅 [如何在 MySQL 中使用索引](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html)。

### <a name="use-explain-for-your-select-queries"></a>对你的选择查询使用说明
使用 `EXPLAIN` 语句获取有关 MySQL 运行查询的内容的见解。 它可以帮助您检测查询中的瓶颈或问题。 请参阅 [如何使用说明来分析查询性能](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)。



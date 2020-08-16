---
title: 应用开发最佳做法-Azure Database for MySQL
description: 了解使用 Azure Database for MySQL 生成应用时的最佳做法
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 36f9cfa2369032351f6ed2948fc0c98c1648bcb6
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2020
ms.locfileid: "88259242"
---
# <a name="best-practices-for-building-applications-with-azure-database-for-mysql"></a>用 Azure Database for MySQL 生成应用程序的最佳做法 

下面是一些最佳做法，可帮助使用可缩短应用程序开发时间的 Azure Database for MySQL 来构建云就绪应用程序。 

## <a name="application-and-database-resource-configuration"></a>应用程序和数据库资源配置

### <a name="application-and-database-in-the-same-region"></a>同一区域中的应用程序和数据库
在 Azure 中部署应用程序时，请确保 **所有依赖项都位于同一区域**  。 跨区域或可用性区域分配实例会产生网络延迟，这可能会影响应用程序的总体性能。 

### <a name="keep-your-mysql-server-secure"></a>保证 MySQL 服务器的安全
MySQL server 应配置为 [安全](https://docs.microsoft.com/azure/mysql/concepts-security) 且不可公开访问。 使用以下任一选项来保护服务器： 
- [防火墙规则](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) 或
- [虚拟网络](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet) 或 
- [专用链接](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)

为安全，必须始终通过 **SSL** 连接到 mysql 服务器，并将 mysql server 和应用程序配置为使用 **tls 1.2**。 请参阅 [如何配置 SSL/TLS](https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security)。 

### <a name="tune-your-server-parameters"></a>优化服务器参数
对于优化这些参数的读繁重的工作负荷，"tmp_table_size 和 max_heap_table_size" 有助于优化性能。 若要计算 tmp_table_size 和 max_heap_table_size 所需的值，请查看每个连接的内存总值和基本内存。 每连接内存参数的总和，不包括 tmp_table_size，并与服务器的总内存帐户结合起来。

若要计算 tmp_table_size 和 max_heap_table_size 可能的最大大小，请使用以下公式：

```(total memory - (base memory + (sum of per-connection memory * # of connections)) / # of connections```

>[!NOTE]
> Total memory 指示服务器在预配的 Vcore 中具有的内存总量。  例如，在常规用途 2 vCore Azure Database for MySQL 服务器中，总内存为 5GB * 2。  在 [定价层](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) 文档中可以找到有关每个层的内存的更多详细信息。
> 基本内存指示在服务器启动时将初始化和分配的内存变量，如 query_cache_size 和 innodb_buffer_pool_size。  每个连接内存（如 sort_buffer_size 和 join_buffer_size）是仅当查询需要时分配的内存。

### <a name="create-a-non-admin-user"></a>创建非管理员用户 
为每个数据库[创建非管理员用户](https://docs.microsoft.com/azure/mysql/howto-create-users)。 通常，用户名标识为数据库名称。

### <a name="resetting-your-password"></a>正在重置密码
你可以使用 Azure 门户为 MySQL 服务器 [重置密码](https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password) 。 

重置生产数据库的服务器密码可能会导致应用程序关闭。 在非高峰时段重置任何生产工作负荷的密码是一种很好的模式，以将对应用程序最终用户的影响降至最低。

## <a name="performance-and-resiliency"></a>性能和复原 
下面是一些可以用来帮助调试应用程序性能问题的工具和模式。

### <a name="enable-slow-query-logs-identify-performance-issues"></a>启用慢查询日志来识别性能问题
你可以在服务器上启用 [慢查询日志](https://docs.microsoft.com/azure/mysql/concepts-server-logs) 和 [审核日志](https://docs.microsoft.com/azure/mysql/concepts-audit-logs) 。 分析慢查询日志有助于识别故障排除的性能瓶颈。 

还可以通过 Azure Monitor 日志、事件中心和存储帐户中的 Azure 诊断日志获取审核日志。 请参阅 [如何排查查询性能问题](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)。

### <a name="use-connection-pooling"></a>使用连接池
管理数据库连接可能会对整个应用程序的性能造成很大的影响。 若要优化性能，必须减少建立连接的次数以及在关键代码路径中建立连接的时间。  使用 [连接池](https://docs.microsoft.com/azure/mysql/concepts-connectivity#access-databases-by-using-connection-pooling-recommended) 连接到 Azure Database for MySQL 以提高复原能力和性能。 

[ProxySQL](https://proxysql.com/)是一种可以有效地用于管理连接的连接池。 使用连接池池可能会降低空闲连接并重复使用现有连接，这有助于避免问题。 请参阅 [如何设置 ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/connecting-efficiently-to-azure-database-for-mysql-with-proxysql/ba-p/1279842) 以了解详细信息。 

### <a name="retry-logic-to-handle-transient-errors"></a>用于处理暂时性错误的重试逻辑
您的应用程序可能会遇到 [暂时性错误](https://docs.microsoft.com/azure/mysql/concepts-connectivity#handling-transient-errors) ，在这种情况下，到数据库的连接会间歇删除或丢失。 在这种情况下，在5-10 秒内重试一次后，服务器会启动并运行。 

要执行重试的好模式是在第一次重试前等待5秒，然后在每次重试后，通过将等待时间增加到60秒即可完成。 限制应用程序认为操作失败的最大重试次数，以便进一步调查。 有关详细信息，请参阅 [如何对连接错误进行故障排除](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-common-connection-issues) 。 

### <a name="enable-read-replication-to-mitigate-failovers"></a>启用读取复制以缓解故障转移
对于故障转移方案，可以使用 [数据复制](https://docs.microsoft.com/azure/mysql/howto-data-in-replication) 。 使用读取副本时，不会发生主服务器和副本服务器之间的自动故障转移。 你将注意到，因为复制是异步的，所以主副本和副本之间的延迟。 网络延迟可能会受到许多因素的影响，例如，在主服务器上运行的工作负荷的繁重程度以及数据中心之间的延迟。 大多数情况下，副本验证在几秒钟到几分钟之间。

## <a name="database-deployment"></a>数据库部署 

### <a name="configure-azure-database-for-mysql-task-in-your-cicd-deployment-pipeline"></a>在 CI/CD 部署管道中配置 Azure database for MySQL 任务
偶尔需要将更改部署到数据库。 在这种情况下，你可以通过 [Azure 管道](https://azure.microsoft.com/services/devops/pipelines/) 创建使用持续集成 (CI) 和持续交付 (CD) ，并使用 [MySQL 服务器](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-mysql-deployment?view=azure-devops) 的任务通过对数据库运行自定义脚本来更新数据库。

### <a name="manual-database-deployment"></a>手动数据库部署 
在手动数据库部署过程中，下面是一种用于最大程度地减少停机时间或降低部署失败风险的好模式： 

1. 使用[mysqldump](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)或[MySQL 工作台](https://dev.mysql.com/doc/workbench/en/wb-admin-export-import-management.html)在新数据库上创建生产数据库的副本 
2. 将新数据库更新为数据库所需的新架构更改或更新。 
3. 使生产数据库处于只读状态。 在完成部署之前，不应在生产数据库上进行写操作。 
4. 在步骤1中用新更新的数据库测试应用程序。
5. 部署应用程序更改，并确保应用程序现在正在使用具有最新更新的新数据库。 
6. 保留旧的生产数据库，以便回滚更改。 然后，你可以评估以删除旧的生产数据库，或在需要时将其导出到 Azure 存储。 

>[!NOTE]
>  - 如果应用程序类似于电子商务应用程序，在该应用程序中，你可能无法将其置于只读状态，请在进行备份后直接在生产数据库上部署更改。  这些更改应在非高峰时间发生，并向应用程序发送低流量，以 minimze 由于某些用户可能会遇到失败的请求而造成的影响。 
>  - 请确保应用程序代码还处理所有失败的请求。

### <a name="use-mysql-native-metrics-to-see-if-your-workload-is-exceeding-in-memory-temporary-table-sizes"></a>使用 MySQL 本机指标来查看工作负荷是否超出内存中临时表大小
使用读取密集型工作负荷时，针对 MySQL 服务器执行的查询可能会超出内存中临时表的大小。 这可能会导致服务器切换到将临时表写入磁盘，这会影响应用程序的性能。 若要确定服务器是否由于超过临时表大小而写入磁盘，请查看以下指标：

```
show global status like 'created_tmp_disk_tables';
show global status like 'created_tmp_tables';
```
Created_tmp_disk_tables 度量值表示在磁盘上创建的表数，而 created_tmp_table 指标会告诉您工作负荷需要在内存中形成多少临时表。 若要确定运行特定查询是否将使用临时表，请在查询上运行说明。 如果查询将使用临时表运行，则 "额外" 列中的详细信息将指示 "使用临时"。

若要使用溢出到磁盘的查询来计算工作负荷的百分比，请在下面的公式中使用指标值：

```(created_tmp_disk_tables / (created_tmp_disk_tables + created_tmp_tables)) * 100```

理想情况下，此百分比应小于25%。 如果看到百分比为25% 或更大，则建议修改两个服务器参数，tmp_table_size 和 max_heap_table_si


## <a name="database-schema-and-queries"></a>数据库架构和查询

下面是生成数据库架构和查询时需要注意的一些提示和技巧。

### <a name="always-use-the-right-datatype-for-your--table-columns"></a>始终对表列使用正确的数据类型
根据要存储的数据类型使用正确的数据类型可以优化存储并减少由于数据类型不正确而导致的任何错误。

### <a name="use-indexes"></a>使用索引
若要避免速度慢的查询，可使用索引。 索引可以帮助快速查找具有特定列的行。 请参阅 [如何在 MySQL 中使用索引](https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html)。

### <a name="explain-your-select-queries"></a>说明你的选择查询
使用 [解释](https://dev.mysql.com/doc/refman/8.0/en/explain.html) 来了解 MySQL 执行查询的操作。 这可以帮助你检测查询中的瓶颈或问题。 请参阅 [如何使用说明来分析查询性能](https://docs.microsoft.com/azure/mysql/howto-troubleshoot-query-performance)。



---
title: 排查复制延迟问题-Azure Database for MySQL
description: 了解如何排查 Azure Database for MySQL 读取副本的复制延迟问题
keywords: mysql，故障排除，复制延迟（秒）
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876931"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>排查 Azure Database for MySQL 中的复制延迟问题

使用 [读取副本](concepts-read-replicas.md) 功能，可以将 Azure Database for MySQL 服务器中的数据复制到只读副本服务器。 读取副本用于通过将读取/报告查询路由到副本服务器来扩大工作负荷。 这减少了在主服务器上的压力，并在应用程序缩放时提高了应用程序的整体性能和延迟。 使用 MySQL 引擎的基于本机二进制日志 (binlog) 文件位置的复制技术以异步方式更新副本。 若要了解有关 binlog 复制的详细信息，请参阅 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。 

辅助读取副本上的复制滞后取决于多种因素，包括但不限于 

- 网络延迟
- 源服务器上的事务量
- 源和辅助读取副本服务器的计算层
- 在主服务器和辅助服务器上运行的查询。 

在本文档中，你将了解如何解决 Azure Database for MySQL 中的复制延迟问题。 此外，您还将了解在副本服务器上增加复制延迟的一些常见原因。

## <a name="replication-concepts"></a>复制概念

当启用二进制日志时，源服务器会将已提交的事务写入到用于复制的二进制日志中。 默认情况下，对于最多支持 16 TB 存储的所有新设置的服务器，二进制日志均处于打开状态。 在副本服务器上，有两个按副本服务器运行的线程，一个线程称为 IO 线程，另一个称为 SQL 线程。

- **IO 线程**连接到源服务器并请求更新的二进制日志。 此线程收到二进制日志更新后，它们将保存在一个名为中继日志的本地日志中的副本服务器上。
- **SQL 线程**读取中继日志，并将数据更改应用于副本服务器上的)  (。

## <a name="monitoring-replication-latency"></a>监视复制延迟

Azure Database for MySQL 提供 [Azure Monitor](concepts-monitoring.md)中的复制滞后时间（秒）。 此指标仅适用于读取副本服务器。 此指标是使用 MySQL 中的 seconds_behind_master 指标计算得出的。 若要了解复制延迟增加的根本原因，请使用 [MySQL 工作台](connect-workbench.md) 或 [Azure Cloud shell](https://shell.azure.com) 连接到副本服务器，并执行以下命令：

 将值替换为实际的副本服务器名称和管理员用户登录名。 Azure Database for MySQL 的管理员用户名要求 "@ \<servername> "：

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  下图显示了 Cloud Shell 终端中的该体验过程
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
  在同一 Azure Cloud Shell 终端中执行以下命令

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  典型的输出如下所示：
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="监视复制延迟&quot;:::


输出中包含很多信息，但通常只需要重点关注以下各列：

|指标|说明|
|---|---|
|Slave_IO_State| IO 线程的当前状态。 正常情况下，如果正在同步，状态将为 &quot;正在等待主节点发送事件&quot;。 但是，如果您看到 &quot;连接到主服务器&quot; 之类的状态，则该副本已失去与主服务器的连接。 检查主机是否正在运行，或防火墙是否正在阻止连接。|
|Master_Log_File| Master 要写入的二进制日志文件。|
|Read_Master_Log_Pos| 表示上述二进制日志文件中的位置。|
|Relay_Master_Log_File| 指示表示副本服务器从主服务器读取的二进制日志文件。|
|Slave_IO_Running| 指示 IO 线程是否正在运行。 它应为 &quot;是&quot;。 如果是 &quot;NO&quot;，则很可能会断开复制。|
|Slave_SQL_Running| 指示 SQL 线程是否正在运行。 它应为 &quot;是&quot;。 如果是 &quot;NO&quot;，则很可能会断开复制。|
|Exec_Master_Log_Pos| 显示副本正在应用 Relay_Master_Log_File 的位置。 如果存在延迟，此位置序列应小于 Read_Master_Log_Pos。|
|Relay_Log_Space|显示中继日志大小的上限。 可以通过查询 &quot;显示全局变量，如&quot; relay_log_space_limit &quot;来检查大小。|
|Seconds_Behind_Master| 以秒为单位显示复制延迟。|
|Last_IO_Errno|显示 IO 线程错误代码（如果有）。 有关这些代码的详细信息，请参阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)。|
|Last_IO_Error| 显示 IO 线程错误消息（如果有）。|
|Last_SQL_Errno|显示 SQL 线程错误代码（如果有）。 有关这些代码的详细信息，请参阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)。|
|Last_SQL_Error|显示 SQL 线程错误消息（如果有）。|
|Slave_SQL_Running_State| 指示当前的 SQL 线程状态。 请注意，在此状态下显示的 &quot;系统锁定" 是正常行为。 正常情况是将状态显示为 "等待从属事务提交"。 它表示副本正在等待 master 更新提交的事务。|

如果 Slave_IO_Running 为 Yes 并且 Slave_SQL_Running 为 Yes，则复制运行正常。 

接下来，需要检查 Last_IO_Errno、Last_IO_Error、Last_SQL_Errno 和 Last_SQL_Error。  这些字段将保存导致 SQL 线程停止的最新错误的错误号和错误消息。 错误号0和空消息意味着没有错误。 必须通过查找 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)中的错误代码来进一步调查错误中的非零值。

## <a name="common-scenarios-for-high-replication-latency"></a>高复制延迟的常见方案

### <a name="network-latency-or-high-cpu-on-source-server"></a>源服务器上的网络延迟或高 cpu

如果观察到以下值，则最常见的复制延迟原因是网络延迟较高，或者源服务器上的 cpu 使用率很高。 在这种情况下，IO 线程正在运行并在主节点上等待。 主 (源服务器) 已写入二进制日志文件 #20，而副本仅接收到文件 #10。 在此方案中，高复制延迟的主要因素是网络速度或源服务器上的 cpu 使用率较高。  在 Azure 中，区域内的网络延迟通常是以毫秒为单位的范围，而跨区域的网络延迟可能会长达几秒钟。 在大多数情况下，由于源服务器上的 cpu 使用率过高导致 IO 线程处理速度缓慢，导致 IO 线程连接到源服务器的时间延迟。 可以通过使用 Azure monitor 监视 cpu 利用率并观察源服务器上的并发连接数来检测这一点。

如果源服务器上的 cpu 使用率较高，则可能的原因可能是网络延迟。 如果你看到的网络延迟时间太长，则建议你查看 " [Azure 状态" 页](https://status.azure.com/status) ，以确保存在非已知的问题或中断。 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>源服务器上的事务繁重的突发

如果观察到以下值，则复制延迟最常见的原因是源服务器上的事务量过高。 在下面的输出中，尽管副本可以检索 master 后面的二进制日志，但副本 IO 线程表明中继日志空间已满。 因此网络速度不会导致延迟，因为副本已经尝试尽快捕获。 相反，更新后的二进制日志大小超出中继日志空间的上限。 若要进一步解决此问题，应在主服务器上启用 [慢查询日志](concepts-server-logs.md) 。 慢速查询日志使你能够标识源服务器上长时间运行的事务。 需要对标识的查询进行优化以减少服务器上的延迟。 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

下面是此类延迟的常见原因：

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>由于源服务器上的数据负载而导致的复制延迟
在某些情况下，源服务器上有每周或每月的数据加载。 遗憾的是，在这种情况下，复制延迟是不可避免的。 在这种情况下，在源服务器上的数据负载完成后，副本服务器最终会进行追赶。


### <a name="slowness-on-the-replica-server"></a>副本服务器上的缓慢

如果观察到以下值，则最常见的原因可能是需要进一步调查的副本服务器上出现的问题。 在此方案中，如输出中所示，IO 和 SQL 线程运行良好，并且副本正在读取与主写入相同的二进制日志文件。 但是，副本服务器上会出现一些延迟，以反映与源服务器相同的事务。 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

下面是此类延迟的常见原因：

#### <a name="no-primary-or-unique-key-on-a-table"></a>表中没有主键或唯一键

Azure Database for MySQL 使用基于行的复制。 对于基于行的复制，主服务器会将事件写入到有关各个表行更改的二进制日志中。 SQL 线程反过来会对副本服务器上的相应表行执行这些更改。 表中的主键或唯一键是复制延迟的常见原因之一。 缺少主键或唯一键将导致 SQL 线程对目标表中的所有行进行扫描，以应用这些更改。

在 MySQL 中，主键是一项关联索引，可确保快速查询性能，因为它不能包含 NULL 值。 通过 InnoDB 存储引擎，表数据以物理方式组织，以执行超高速度的查找，并基于主键进行排序。 因此，建议在创建副本服务器之前，在源服务器的表中添加主键。 在这种情况下，你需要在源服务器上添加主键并重新创建读取副本以帮助提高复制延迟。

您可以使用以下查询来确定在源服务器上缺少主键的表：

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>由于在副本服务器上运行长时间运行的查询而产生的复制延迟

副本服务器上的工作负荷可能会阻止 SQL 线程跟上 IO 线程。 如果副本服务器上有长时间运行的查询，则这是较高复制延迟的常见原因之一。 在这种情况下，应在副本服务器上启用 [慢查询日志](concepts-server-logs.md) ，以帮助解决此问题。 缓慢查询可能会增加资源使用量或降低服务器的速度，因此副本不能与主服务器进行追赶。 在此方案中，需要优化慢速查询。 更快的查询可防止阻止 SQL 线程，并显著提高复制延迟。


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>源服务器上的 DDL 查询导致的复制延迟
如果存在长时间运行的 DDL 命令，如在源服务器上执行的 [ALTER TABLE](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) ，并假设执行时间为1小时。 在此期间，可能会在源服务器上并行运行上千个其他查询。 当 DDL 复制到副本时，为确保数据库的一致性，MySQL 引擎必须在单个复制线程中运行 DDL。 因此，所有其他复制的查询将被阻止，并且需要等待一小时或更多时间，直到在副本服务器上完成 DDL 操作。 无论联机 DDL 操作是什么，都是如此。 使用 DDL 操作时，复制应会看到增加的复制延迟。

如果在源服务器上启用了 [慢速查询日志](concepts-server-logs.md) ，则可以通过查看慢速查询日志来检测是否在源服务器上执行了 DDL 命令。 尽管索引删除、重命名和创建应使用 ALTER TABLE 的就地算法，但它可能涉及到复制表数据和重新生成表。 通常情况下，支持就地算法并发 DML，但在操作的准备和执行阶段，可能会短暂地考虑对表的排他元数据锁。 因此，对于 CREATE INDEX 语句，子句算法和 LOCK 可用于影响表复制方法和用于读取和写入的并发级别，不过添加全文或空间索引仍会阻止 DML 操作。 请参阅下面的示例：使用算法和锁子句创建索引：

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

遗憾的是，对于需要锁的 DDL 语句，不能避免复制延迟，而应在非高峰时段执行这些类型的 DDL 操作，例如在夜间期间，以减少潜在影响。

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>副本服务器低级 SKU 导致的复制延迟

在 Azure Database for MySQL 将用与主服务器相同的服务器配置创建读取副本。 副本服务器配置在创建后可以更改。 但是，如果将降级副本服务器，则工作负荷会导致更高的资源消耗，进而导致复制延迟。 这可以通过监视副本服务器的 CPU 和内存消耗 Azure Monitor 来观察。 在此方案中，建议副本服务器的配置应保留为与源相同或更大的值，以确保副本能够与主副本保持同步。

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>使用源服务器上的服务器参数优化提高复制延迟

在 Azure Database for MySQL 中，复制经过优化，可在默认情况下使用副本上的并行线程运行。 对于副本服务器无法弥补的源服务器上的高并发性工作负荷，可以通过在源服务器上配置参数 binlog_group_commit_sync_delay 来改进复制延迟。 此参数控制在同步二进制日志文件之前二进制日志提交等待的微秒数。 优点在于，它不会立即应用每个提交的事务，而是批量发送二进制日志更新。 这可以减少副本上的 IO，有助于提高性能。 在这种情况下，将 binlog_group_commit_sync_delay 设置为1000或者监视复制延迟可能会很有用。 应慎重设置此参数，并仅将其用于高并发工作负荷。 对于包含大量单独事务的低并发方案，设置 binlog_group_commit_sync_delay 可能会增加延迟，因为 IO 线程正在等待大容量二进制日志更新，而只提交少量事务。 

## <a name="next-steps"></a>后续步骤
了解有关 [MySQL binlog 复制概述](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)的详细信息。

---
title: "如何在用于 MySQL 的 Azure 数据库中配置服务器参数 | Microsoft Docs"
description: "本文介绍如何使用 Azure 门户在用于 MySQL 的 Azure 数据库中配置可用的服务器参数。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/19/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 718bbf359253849fbc989c563ffd6d1099f92348
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017

---

# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>如何使用 Azure 门户在用于 MySQL 的 Azure 数据库中配置服务器参数

用于 MySQL 的 Azure 数据库支持配置某些服务器参数。 本文介绍如何使用 Azure 门户配置这些参数，并列出支持的参数、默认值和有效值的范围。 并非所有服务器参数都可调整。 仅支持此处列出的参数。

## <a name="navigate-to-server-parameters-blade-on-azure-portal"></a>在 Azure 门户中导航到“服务器参数”边栏选项卡

登录到 Azure 门户，然后单击用于 MySQL 的 Azure 数据库服务器名称。 在“设置”部分下面，单击“服务器参数”打开用于 MySQL 的 Azure 数据库的“服务器参数”边栏选项卡。

![Azure 门户中的服务器参数边栏选项卡](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>可配置的服务器参数列表

下表列出当前支持的服务器参数。 可根据应用程序要求配置这些参数。

> [!div class="mx-tableFixed"]
|参数名称|默认值|范围|说明|
|---|---|---|---|
|binlog_group_commit_sync_delay|1000|0, 11-1000000|控制在将二进制日志文件同步到磁盘之前，二进制日志提交操作需要等待多少微秒。|
|binlog_group_commit_sync_no_delay_count|0|0-1000000|在中止 binlog-group-commit-sync-delay 指定的当前延迟之前，需要等待完成的事务的最大数目。|
|character_set_server|LATIN1|BIG5、UTF8MB4，等等。|使用 charset_name 作为默认的服务器字符集。|
|div_precision_increment|4|0-30|要将除法运算结果的小数位数增加的个数。|
|group_concat_max_len|1024|4-16777216|GROUP_CONCAT() 允许的最大结果长度，以字节为单位。|
|innodb_adaptive_hash_index|亮起|ON、OFF|是启用还是禁用 innodb 自适应哈希索引。|
|innodb_lock_wait_timeout|50|1-3600|在放弃之前，InnoDB 事务需要等待行锁的秒数。|
|interactive_timeout|1800|10-1800|在关闭服务器之前，服务器需要等待交互式连接上出现活动的秒数。|
|log_bin_trust_function_creators|熄灭|ON、OFF|启用二进制日志记录时，将应用此变量。 它控制是否可以信任存储函数创建器创建会导致将不安全事件写入二进制日志的存储函数。|
|log_queries_not_using_indexes|熄灭|ON、OFF|记录预期会将所有行检索到慢速查询日志中的查询。|
|log_slow_admin_statements|熄灭|ON、OFF|在写入慢速查询日志的语句中包含慢速管理语句。|
|log_throttle_queries_not_using_indexes|0|0-4294967295|限制每分钟可写入慢速查询日志的此类查询的数目。|
|long_query_time|10|1-1E+100|如果查询时间超过此秒数，则服务器会递增 Slow_queries 状态变量。|
|max_allowed_packet|536870912|1024-1073741824|mysql_stmt_send_long_data() C API 函数发送的一个数据包、任何生成的字符串/中间字符串或任何参数的最大大小。|
|min_examined_row_limit|0|0-18446744073709551615|在慢速查询日志中记录已超过配置行数的查询。 |
|server_id|3293747068|1000-4294967295|在复制中用来为每个主设备和从设备提供唯一标识的服务器 ID。|
|slave_net_timeout|60|30-3600|在从设备将连接视为已中断、中止读取并尝试重新连接之前，需要等待主设备提供更多数据的秒数。|
|slow_query_log|熄灭|ON、OFF|启用或禁用慢速查询日志。|
|sql_mode|已选择 0|ALLOW_INVALID_DATES、IGNORE_SPACE，等等。|当前的服务器 SQL 模式。|
|time_zone|SYSTEM|示例：-8:00, +05:30|服务器时区。|
|wait_timeout|120|60-240|在关闭服务器之前，服务器需要等待非交互式连接上出现活动的秒数。|

## <a name="next-steps"></a>后续步骤
- [Azure Database for MySQL 的连接库](concepts-connection-libraries.md)


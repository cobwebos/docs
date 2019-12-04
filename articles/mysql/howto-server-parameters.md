---
title: 配置服务器参数-Azure 门户-Azure Database for MySQL
description: 本文介绍如何使用 Azure 门户在适用于 MySQL 的 Azure 数据库中配置 MySQL 服务器参数。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: f803b7cccf3520c309e6b33d99b5565cfc4fdd01
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764910"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>如何使用 Azure 门户在适用于 MySQL 的 Azure 数据库中配置服务器参数

用于 MySQL 的 Azure 数据库支持配置某些服务器参数。 本文介绍如何使用 Azure 门户配置这些参数。 并非所有服务器参数都可调整。

## <a name="navigate-to-server-parameters-on-azure-portal"></a>在 Azure 门户中导航到“服务器参数”

1. 登录到 Azure 门户，然后定位到适用于 MySQL 服务器的 Azure 数据库。
2. 在“设置”部分下，单击“服务器参数”，打开 Azure Database for MySQL 服务器的“服务器参数”页。
![Azure 门户中的服务器参数页](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. 定位需要调整的任何设置。 查看“说明”列，了解用途和允许的值。
![枚举下拉按钮](./media/howto-server-parameters/3-toggle_parameter.png)
4. 单击“保存”，保存更改。
![保存或放弃更改](./media/howto-server-parameters/4-save_parameters.png)
5. 保存参数的新值后，随时可以通过选择“全部重置为默认设置”，将所有设置还原为默认值。
![全部重置为默认设置](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>可配置的服务器参数列表

受支持服务器参数的列表还在不断增加。 在 Azure 门户中使用服务器参数选项卡，以根据应用程序要求获取定义并配置服务器参数。

## <a name="non-configurable-server-parameters"></a>不可配置的服务器参数

InnoDB 缓冲池的大小不能配置并绑定到[定价层](concepts-service-tiers.md)。

|**定价层**|**vCore(s)**|**InnoDB 缓冲池大小（MB <br>）（支持多达 4 TB 存储的服务器）**| **InnoDB 缓冲池大小（MB <br>）（支持高达 16 TB 存储的服务器）**|
|:---|---:|---:|---:|
|基本| 第| 832| |
|基本| 2| 2560| |
|常规用途| 2| 3584| 7168|
|常规用途| 4| 7680| 15360|
|常规用途| 8| 15360| 30720|
|常规用途| 16| 31232| 62464|
|常规用途| 32| 62976| 125952|
|常规用途| 64| 125952| 251904|
|内存优化| 2| 7168| 14336|
|内存优化| 4| 15360| 30720|
|内存优化| 8| 30720| 61440|
|内存优化| 16| 62464| 124928|
|内存优化| 32| 125952| 251904|

以下附加服务器参数不可在系统中配置：

|**Parameter**|**固定值**|
| :------------------------ | :-------- |
|基本层中的 innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|第|
|sync_binlog|第|
|innodb_log_file_size|512MB|

在版本 [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) 和 [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html) 中，上表中未列出的其他服务器参数将设置为其 MySQL 现成默认值。

## <a name="working-with-the-time-zone-parameter"></a>使用时区参数

### <a name="populating-the-time-zone-tables"></a>填充时区表

可以通过从 MySQL 命令行或 MySQL Workbench 等工具调用 `az_load_timezone` 存储过程，填充服务器上的时区表。

> [!NOTE]
> 如果正在运行 MySQL Workbench 中的 `az_load_timezone` 命令，可能需要先使用 `SET SQL_SAFE_UPDATES=0;` 关闭安全更新模式。

```sql
CALL mysql.az_load_timezone();
```

要查看可用的时区值，请运行以下命令：

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>设置全局级时区

可以从 Azure 门户中的“服务器参数”页设置全局级时区。 下面将全局时区值设置为“美国/太平洋”。

![设置时区参数](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>设置会话级时区

可以通过从 MySQL 命令行或 MySQL Workbench 等工具运行 `SET time_zone` 命令来设置会话级时区。 以下示例将时区设置为“美国/太平洋”时区。

```sql
SET time_zone = 'US/Pacific';
```

若要了解[日期和时间函数](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)，请参阅 MySQL 文档。

## <a name="next-steps"></a>后续步骤

- [适用于 MySQL 的 Azure 数据库的连接库](concepts-connection-libraries.md)。

---
title: 配置服务器参数 - Azure 门户 - Azure Database for MariaDB
description: 本文介绍如何使用 Azure 门户在 Azure Database for MariaDB 中配置 MariaDB 服务器参数。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/11/2020
ms.openlocfilehash: ae9878ba17f05b2d843e18c921c87c44b9396827
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738305"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-the-azure-portal"></a>使用 Azure 门户在 Azure Database for MariaDB 中配置服务器参数

Azure Database for MariaDB 支持配置某些服务器参数。 本文介绍如何使用 Azure 门户配置这些参数。 并非所有服务器参数都可调整。

## <a name="configure-server-parameters"></a>配置服务器参数

1. 登录到 Azure 门户，然后定位到 Azure Database for MariaDB 服务器。
2. 在“设置”部分下，单击“服务器参数”，打开 Azure Database for MariaDB 服务器的“服务器参数”页。
![Azure 门户中的服务器参数页](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. 定位需要调整的任何设置。 查看“说明”列，了解用途和允许的值。
![枚举下拉按钮](./media/howto-server-parameters/3-toggle_parameter.png)
4. 单击“保存”，保存更改。
![保存或放弃更改](./media/howto-server-parameters/4-save_parameters.png)
5. 保存参数的新值后，随时可以通过选择“全部重置为默认设置”，将所有设置还原为默认值。
![全部重置为默认设置](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="setting-parameters-not-listed"></a>未列出设置参数

如果 Azure 门户中未列出您要更新的服务器参数，则可以选择使用在连接级别设置参数 `init_connect` 。 这会为每个连接到服务器的客户端设置服务器参数。 

1. 在“设置”部分下，单击“服务器参数”，打开 Azure Database for MariaDB 服务器的“服务器参数”页。
2. 搜索`init_connect`
3. 按以下格式添加服务器参数： `SET parameter_name=YOUR_DESIRED_VALUE` 值列中的 "值"。

    例如，你可以通过将设置为来更改服务器的字符集 `init_connect``SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. 单击“保存”以保存更改。

## <a name="working-with-the-time-zone-parameter"></a>使用时区参数

### <a name="populating-the-time-zone-tables"></a>填充时区表

可以通过从 MySQL 命令行或 MySQL Workbench 等工具调用 `mysql.az_load_timezone` 存储过程，填充服务器上的时区表。

> [!NOTE]
> 如果正在运行 MySQL Workbench 中的 `mysql.az_load_timezone` 命令，可能需要先使用 `SET SQL_SAFE_UPDATES=0;` 关闭安全更新模式。

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> 应重启服务器，确保正确填充时区表。 若要重启服务器，请使用 [Azure 门户](howto-restart-server-portal.md)或 [CLI](howto-restart-server-cli.md)。
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

若要了解[日期和时间函数](https://mariadb.com/kb/en/library/convert_tz/)，请参阅 MariaDB 文档。

## <a name="next-steps"></a>后续步骤

- 了解有关[服务器参数](concepts-server-parameters.md)的详细信息
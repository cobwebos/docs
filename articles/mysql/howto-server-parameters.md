---
title: 配置服务器参数 - Azure 门户 - Azure Database for MySQL
description: 本文介绍如何使用 Azure 门户在适用于 MySQL 的 Azure 数据库中配置 MySQL 服务器参数。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: c28f0edafd72794a60ef577fc3177e4436157950
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631471"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>使用 Azure 门户在 Azure Database for MySQL 中配置服务器参数

用于 MySQL 的 Azure 数据库支持配置某些服务器参数。 本文介绍如何使用 Azure 门户配置这些参数。 并非所有服务器参数都可调整。

>[!Note]
> 可以在服务器级别全局更新服务器参数，使用 [Azure CLI](./howto-configure-server-parameters-using-cli.md)、 [PowerShell](./howto-configure-server-parameters-using-powershell.md)或 [Azure 门户](./howto-server-parameters.md)。

## <a name="configure-server-parameters"></a>配置服务器参数

1. 登录到 [Azure 门户](https://portal.azure.com)，然后找到 Azure Database for MySQL 服务器。
2. 在“设置”部分下，单击“服务器参数”，打开 Azure Database for MySQL 服务器的“服务器参数”页。
:::image type="content" source="./media/howto-server-parameters/auzre-portal-server-parameters.png" alt-text="Azure 门户中的服务器参数页":::
3. 定位需要调整的任何设置。 查看“说明”列，了解用途和允许的值。
:::image type="content" source="./media/howto-server-parameters/3-toggle_parameter.png" alt-text="Azure 门户中的服务器参数页":::
4. 单击“保存”，保存更改。
:::image type="content" source="./media/howto-server-parameters/4-save_parameters.png" alt-text="Azure 门户中的服务器参数页":::
5. 保存参数的新值后，随时可以通过选择“全部重置为默认设置”，将所有设置还原为默认值。
:::image type="content" source="./media/howto-server-parameters/5-reset_parameters.png" alt-text="Azure 门户中的服务器参数页":::

## <a name="setting-parameters-not-listed"></a>设置参数未列出

如果 Azure 门户中未列出你要更新的服务器参数，则可以选择性地使用 `init_connect` 在连接级别设置参数。 此项可为每个连接到服务器的客户端设置服务器参数。 

1. 在“设置”部分下，单击“服务器参数”，打开 Azure Database for MySQL 服务器的“服务器参数”页。
2. 搜索 `init_connect`
3. 在 value 列的 value 中添加服务器参数，格式为 `SET parameter_name=YOUR_DESIRED_VALUE`。

    例如，可以通过将 `init_connect` 设置为 `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;` 来更改服务器的字符集
4.  以保存更改。

>[!Note]
> `init_connect` 可用于在会话级别更改无需 SUPER 权限的参数。 若要验证是否可以使用 `init_connect` 设置参数，请执行 `set session parameter_name=YOUR_DESIRED_VALUE;` 命令，如果出现“拒绝访问；需要 SUPER 权限”错误，则无法使用“init_connect”设置参数。

## <a name="working-with-the-time-zone-parameter"></a>使用时区参数

### <a name="populating-the-time-zone-tables"></a>填充时区表

可以通过从 MySQL 命令行或 MySQL Workbench 等工具调用 `mysql.az_load_timezone` 存储过程，填充服务器上的时区表。

> [!NOTE]
> 如果正在运行 MySQL Workbench 中的 `mysql.az_load_timezone` 命令，可能需要先使用 `SET SQL_SAFE_UPDATES=0;` 关闭安全更新模式。

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> 应重启服务器，确保正确填充时区表。 要重启服务器，请使用 [Azure 门户](howto-restart-server-portal.md)或 [CLI](howto-restart-server-cli.md)。

要查看可用的时区值，请运行以下命令：

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>设置全局级时区

可以从 Azure 门户中的“服务器参数”页设置全局级时区。 下面将全局时区值设置为“美国/太平洋”。

:::image type="content" source="./media/howto-server-parameters/timezone.png" alt-text="Azure 门户中的服务器参数页":::

### <a name="setting-the-session-level-time-zone"></a>设置会话级时区

可以通过从 MySQL 命令行或 MySQL Workbench 等工具运行 `SET time_zone` 命令来设置会话级时区。 以下示例将时区设置为“美国/太平洋”时区。

```sql
SET time_zone = 'US/Pacific';
```

若要了解[日期和时间函数](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)，请参阅 MySQL 文档。

## <a name="next-steps"></a>后续步骤

- [Azure Database for MySQL 的连接库](concepts-connection-libraries.md)。

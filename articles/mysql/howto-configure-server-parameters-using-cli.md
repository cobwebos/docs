---
title: 在 Azure Database for MySQL 中配置服务参数
description: 本文介绍如何使用 Azure CLI 命令行实用工具在 Azure Database for MySQL 中配置服务参数。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 07/18/2018
ms.openlocfilehash: 637e2d27e92c1a2618fcf8b524e475a4d2f88f12
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136366"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>使用 Azure CLI 自定义服务器配置参数
可以使用 Azure CLI、Azure 命令行实用工具来列出、显示和更新 Azure Database for MySQL 服务器的配置参数。 在服务器级别会公开引擎配置的一个子集，并可以进行修改。 

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) 命令行实用工具或在浏览器中使用 Azure Cloud Shell。

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>列出 Azure Database for MySQL 服务器的服务器配置参数
若要列出服务器中的所有可修改参数及其值，请运行 [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) 命令。

可以列出资源组“myresourcegroup”下服务器 mydemoserver.mysql.database.azure.com 的服务器配置参数。
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
有关每个列出参数的定义，请参阅[服务器系统变量](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)上的 MySQL 引用部分。

## <a name="show-server-configuration-parameter-details"></a>显示服务器配置参数详细信息
若要显示服务器的某个特定配置参数的详细信息，请运行 [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) 命令。

本示例显示了资源组“myresourcegroup”下服务器 mydemoserver.mysql.database.azure.com 的服务器配置参数 **slow\_query\_log** 的详细信息。
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>修改服务器配置参数值
此外，你还可以修改某个服务器配置参数的值，这会更新 MySQL 服务器引擎的基础配置值。 若要更新配置，请使用 [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) 命令。 

更新资源组“myresourcegroup”下服务器 mydemoserver.mysql.database.azure.com 的服务器配置参数 **slow\_query\_log**。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
若要重置配置参数的值，省去可选的 `--value` 参数，服务将应用默认值。 对于上述示例，它将如下所示：
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
此代码会将 slow\_query\_log 配置重置为默认值 OFF。 

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

可以使用 [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) 命令来设置全局级时区。

以下命令将资源组 myresourcegroup 下的服务器 mydemoserver.mysql.database.azure.com 的 time\_zone 服务器配置参数更新为“美国/太平洋”。

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>设置会话级时区

可以通过从 MySQL 命令行或 MySQL Workbench 等工具运行 `SET time_zone` 命令来设置会话级时区。 以下示例将时区设置为“美国/太平洋”时区。  

```sql
SET time_zone = 'US/Pacific';
```

若要了解[日期和时间函数](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)，请参阅 MySQL 文档。


## <a name="next-steps"></a>后续步骤

- 如何配置 [Azure 门户中的服务器参数](howto-server-parameters.md)
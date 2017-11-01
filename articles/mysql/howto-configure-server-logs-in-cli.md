---
title: "使用 Azure CLI 访问 Azure Database for MySQL 中的服务器日志 | Microsoft Docs"
description: "本文介绍了如何使用 Azure CLI 命令行实用工具访问 Azure Database for MySQL 中的服务器日志。"
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/18/2017
ms.openlocfilehash: 6ee2c2c6e6cff824d3167ea600a1ddc778ad011b
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>使用 Azure CLI 配置和访问服务器日志
可以使用 Azure CLI（Azure 的命令行实用工具）列出和下载 Azure Database for MySQL 服务器日志。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli)，或在浏览器中使用 Azure Cloud Shell。

## <a name="configure-logging-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 配置日志记录
可以对服务器进行配置来访问 MySQL 慢查询日志。
1. 通过将 **slow\_query\_log** 参数设置为 ON 启用日志记录。
2. 调整其他参数，例如 **long\_query\_time** 和 **log\_slow\_admin\_statements**。

若要了解如何通过 Azure CLI 设置这些参数的值，请参阅[如何配置服务器参数](howto-configure-server-parameters-using-cli.md)。

例如，以下 CLI 命令将启用慢查询日志、将长查询时间设置为 10 秒并禁用对慢管理语句的日志记录。 最后，它将列出配置选项供复查。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server myserver4demo --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server myserver4demo --value OFF
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>列出 Azure Database for MySQL 服务器的日志
若要列出服务器的可用日志文件，请运行 [az mysql server-logs list](/cli/azure/mysql/server-logs#list) 命令。

可以列出资源组 **myresourcegroup** 下的服务器 **myserver4demo.mysql.database.azure.com** 的日志文件，并将其定向到名为 **log\_files\_list.txt** 的文本文件。
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server myserver4demo > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>从服务器下载日志
可以使用 [az mysql server-logs download](/cli/azure/mysql/server-logs#download) 命令下载服务器的各个日志文件。 

此示例演示了将资源组 **myresourcegroup** 下的服务器 **myserver4demo.mysql.database.azure.com** 的特定日志文件下载到本地环境。
```azurecli-interactive
az mysql server-logs download --name 20170414-myserver4demo-mysql.log --resource-group myresourcegroup --server myserver4demo
```

## <a name="next-steps"></a>后续步骤
- 了解 [Azure Database for MySQL 中的服务器日志](concepts-server-logs.md)

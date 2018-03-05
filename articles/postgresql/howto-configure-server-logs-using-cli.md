---
title: "使用 Azure CLI 配置和访问 PostgreSQL 的服务器日志"
description: "本文介绍如何使用 Azure CLI 命令行在 Azure Database for PostgreSQL 中配置和访问服务器日志。"
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e12a8907b641b4591ed5ff9fdd5d8458eb75525e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>使用 Azure CLI 配置和访问服务器日志
可以使用命令行接口 (Azure CLI) 下载 PostgreSQL 服务器错误日志。 但不支持访问事务日志。 

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-azure-cli.md)
- 安装 [Azure CLI 2.0](/cli/azure/install-azure-cli) 命令行实用工具或使用浏览器中的 Azure Cloud Shell。

## <a name="configure-logging-for-azure-database-for-postgresql"></a>为 Azure Database for PostgreSQL 配置日志记录
可以将服务器配置为访问查询日志和错误日志。 错误日志包含自动清空、连接和检查点等信息。
1. 启用日志
2. 更新 log\_statement 和 log\_min\_duration\_statement 以启用查询日志
3. 更新保留期

请参阅[自定义服务器配置参数](howto-configure-server-parameters-using-cli.md)，了解详细信息。

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>列出 Azure Database for PostgreSQL 服务器的日志
若要列出服务器的可用日志文件，请运行 [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) 命令。

可以列出资源组“myresourcegroup”下服务器 mydemoserver.postgres.database.azure.com 的日志文件，并将其定向到名为 log\_files\_list.txt 的文本文件。
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>从服务器将日志下载到本地
使用 [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) 命令可下载服务器的单独日志文件。 

此示例演示将资源组“myresourcegroup”下服务器 mydemoserver.postgres.database.azure.com 的特定日志文件下载到本地环境。
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>后续步骤
- 若要了解有关服务器日志的详细信息，请参阅 [Azure Database for PostgreSQL 中的服务器日志](concepts-server-logs.md)
- 有关服务器参数的详细信息，请参阅[使用 Azure CLI 自定义服务器配置参数](howto-configure-server-parameters-using-cli.md)

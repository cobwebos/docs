---
title: 访问慢速查询 Azure Database for MySQL 中日志使用 Azure CLI
description: 本文介绍如何使用 Azure CLI 访问 for MySQL 的 Azure 数据库中的慢查询日志。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: e6d25a4d8b470580626cab4a84f9d912a3f79f75
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612641"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>配置和使用 Azure CLI 访问慢查询日志
可以使用 Azure CLI、 Azure 命令行实用程序来下载 Azure Database for MySQL 慢查询日志。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 或浏览器中的 Azure Cloud Shell

## <a name="configure-logging"></a>配置日志记录
通过执行下列步骤，可以对服务器进行配置以访问 MySQL 慢查询日志：
1. 通过设置启用慢查询日志记录**慢速\_查询\_日志**参数为 ON。
2. 调整其他参数，例如 **long\_query\_time**  和  **log\_slow\_admin\_statements**。

若要了解如何通过 Azure CLI 设置这些参数的值，请参阅[如何配置服务器参数](howto-configure-server-parameters-using-cli.md)。

例如，以下 CLI 命令将启用慢查询日志、将长查询时间设置为 10 秒并禁用慢管理语句的日志记录。 最后，它将列出配置选项供复查。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>列出 Azure Database for MySQL 服务器的日志
若要列出可用的慢查询日志文件以供你的服务器，请运行[az mysql 服务器日志列表](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)命令。

可以列出资源组“myresourcegroup”  下的服务器“mydemoserver.mysql.database.azure.com”  的日志文件。 然后在日志文件列表中找到名为“log\_files\_list.txt”的文本文件  。
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>从服务器下载日志
使用 [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) 命令可下载服务器的单个日志文件。 

使用下列示例，可以将资源组“myresourcegroup”下服务器 mydemoserver.mysql.database.azure.com 的特定日志文件下载到本地环境   。
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>后续步骤
- 了解如何[慢查询中的日志 Azure Database for MySQL](concepts-server-logs.md)。

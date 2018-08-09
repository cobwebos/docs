---
title: 使用 Azure CLI 访问 Azure Database for MySQL 中的服务器日志
description: 本文介绍了如何使用 Azure CLI 命令行实用工具访问 Azure Database for MySQL 中的服务器日志。
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 57b72ded77484dc1c8ca4c62811b62e171365db4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423451"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>使用 Azure CLI 配置和访问服务器日志
可以使用 Azure CLI（Azure 的命令行实用工具）下载 Azure Database for MySQL 服务器日志。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli)，或浏览器中的 Azure Cloud Shell

## <a name="configure-logging-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 配置日志记录
通过执行下列步骤，可以对服务器进行配置以访问 MySQL 慢查询日志：
1. 通过将 slow\_query\_log 参数设置为 ON 启用日志记录。
2. 调整其他参数，例如 long\_query\_time 和 log\_slow\_admin\_statements。

若要了解如何通过 Azure CLI 设置这些参数的值，请参阅[如何配置服务器参数](howto-configure-server-parameters-using-cli.md)。 

例如，以下 CLI 命令将启用慢查询日志、将长查询时间设置为 10 秒并禁用慢管理语句的日志记录。 最后，它将列出配置选项供复查。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>列出 Azure Database for MySQL 服务器的日志
若要列出服务器的可用日志文件，请运行 [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) 命令。

可以列出资源组“myresourcegroup”下的服务器“mydemoserver.mysql.database.azure.com”的日志文件。 然后在日志文件列表中找到名为“log\_files\_list.txt”的文本文件。
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>从服务器下载日志
使用 [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) 命令可下载服务器的单独日志文件。 

使用下列示例，可以将资源组“myresourcegroup”下服务器 mydemoserver.mysql.database.azure.com 的特定日志文件下载到本地环境。
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>后续步骤
- 了解 [Azure Database for MySQL 中的服务器日志](concepts-server-logs.md)。

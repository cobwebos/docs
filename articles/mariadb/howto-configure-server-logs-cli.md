---
title: 使用 Azure CLI 访问 Azure Database for MariaDB 中的服务器日志
description: 本文介绍如何使用 Azure CLI 命令行实用工具访问 Azure Database for MariaDB 中的服务器日志。
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 9fff9f13e5ce6bf8a7805f7794d3e71eb4030104
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065687"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>使用 Azure CLI 配置和访问服务器日志
可以使用 Azure CLI（Azure 的命令行实用工具）下载 Azure Database for MariaDB 服务器日志。

## <a name="prerequisites"></a>必备组件
若要逐步执行本操作方法指南，需要：
- [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 或浏览器中的 Azure Cloud Shell

## <a name="configure-logging-for-azure-database-for-mariadb"></a>为 Azure Database for MariaDB 配置日志记录
通过执行下列步骤，可以对服务器进行配置以访问 MariaDB 慢查询日志：
1. 通过将 slow\_query\_log  参数设置为 ON 启用日志记录。
2. 调整其他参数，例如 **long\_query\_time**  和  **log\_slow\_admin\_statements**。

若要了解如何通过 Azure CLI 设置这些参数的值，请参阅[如何配置服务器参数](howto-configure-server-parameters-cli.md)。

例如，以下 CLI 命令将启用慢查询日志、将长查询时间设置为 10 秒并禁用慢管理语句的日志记录。 最后，它将列出配置选项供复查。
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>列出 Azure Database for MariaDB 服务器的日志
若要列出可用的慢查询日志文件以供你的服务器，请运行[az mariadb 服务器日志列表](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)命令。

可以列出资源组“myresourcegroup”  下的服务器“mydemoserver.mariadb.database.azure.com”  的日志文件。 然后在日志文件列表中找到名为“log\_files\_list.txt”的文本文件  。
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>从服务器下载日志
使用 [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) 命令可下载服务器的单个日志文件。

使用下列示例，可以将资源组“myresourcegroup”下服务器 mydemoserver.mariadb.database.azure.com 的特定日志文件下载到本地环境   。
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>后续步骤
- 了解如何[慢查询 Azure Database for 中日志 MariaDB](concepts-server-logs.md)。

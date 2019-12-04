---
title: 管理日志-Azure CLI-Azure Database for PostgreSQL-单一服务器
description: 本文介绍如何使用 Azure CLI 在 Azure Database for PostgreSQL 单服务器中配置和访问服务器日志（.log 文件）。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: be679be91d49516bd2f6c672eb53640cfad2ae2a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763566"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>使用 Azure CLI 配置和访问服务器日志
可以使用命令行接口 (Azure CLI) 下载 PostgreSQL 服务器错误日志。 但是，访问事务日志不受支持。 

## <a name="prerequisites"></a>必备组件
若要逐步执行本操作方法指南，需要：
- [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 命令行实用工具或浏览器中的 Azure Cloud Shell

## <a name="configure-logging"></a>配置日志记录
可以将服务器配置为访问查询日志和错误日志。 错误日志包含自动清空、连接和检查点等信息。
1. 启用日志。
2. 要启动日期，请更新 log\_statement 和 log\_min\_duration\_statement。
3. 更新保留期。

请参阅[自定义服务器配置参数](howto-configure-server-parameters-using-cli.md)，了解详细信息。

## <a name="list-logs"></a>列出日志
若要列出服务器的可用日志文件，请运行 [az postgres server-logs list](/cli/azure/postgres/server-logs) 命令。

可以列出资源组“myresourcegroup”下的服务器“mydemoserver.postgres.database.azure.com”的日志文件。 然后在日志文件列表中找到名为“log\_files\_list.txt”的文本文件。
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>从服务器将日志下载到本地
使用 [az postgres server-logs download](/cli/azure/postgres/server-logs) 命令可下载服务器的单个日志文件。 

使用下列示例，可以将资源组“myresourcegroup”下服务器 mydemoserver.postgres.database.azure.com 的特定日志文件下载到本地环境。
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>后续步骤
- 若要了解有关服务器日志的详细信息，请参阅 [Azure Database for PostgreSQL 中的服务器日志](concepts-server-logs.md)。
- 有关服务器参数的详细信息，请参阅[使用 Azure CLI 自定义服务器配置参数](howto-configure-server-parameters-using-cli.md)。

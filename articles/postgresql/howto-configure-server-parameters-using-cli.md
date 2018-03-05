---
title: "在 Azure Database for PostgreSQL 中配置服务参数"
description: "本文介绍如何使用 Azure CLI 命令行在 Azure Database for PostgreSQL 中配置服务参数。"
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: df32578c92b1973568c9b811cb7f9e9db067915f
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>使用 Azure CLI 自定义服务器配置参数
可以使用命令行接口 (Azure CLI) 列出、显示和更新 Azure PostgreSQL 服务器的配置参数。 会在服务器级别公开引擎配置的一个子集，并且可以进行修改。 

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- 按照[创建 Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md) 创建 Azure Database for PostgreSQL 服务器和数据库
- 将 [Azure CLI 2.0](/cli/azure/install-azure-cli) 命令行接口安装在计算机上或通过浏览器在 Azure 门户中使用 [Azure Cloud Shell](../cloud-shell/overview.md)。

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>列出 Azure Database for PostgreSQL 服务器的服务器配置参数
若要列出服务器中的所有可修改参数及其值，请运行 [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) 命令。

可以列出资源组“myresourcegroup”下服务器 mydemoserver.postgres.database.azure.com 的服务器配置参数。
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>显示服务器配置参数详细信息
若要显示服务器的某个特定配置参数的详细信息，请运行 [az postgres server configuration show](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show) 命令。

此示例显示了资源组“myresourcegroup”下服务器 mydemoserver.postgres.database.azure.com 的服务器配置参数 log\_min\_messages 的详细信息。
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>修改服务器配置参数值
还可以修改某个服务器配置参数的值，这会更新 PostgreSQL 服务器引擎的基础配置值。 若要更新配置，请使用 [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) 命令。 

更新资源组“myresourcegroup”下服务器 mydemoserver.postgres.database.azure.com 的服务器配置参数 log\_min\_message。
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
若要重置配置参数的值，只需选择省略可选的 `--value` 参数，服务将应用默认值。 以上述示例为例，它类似于下面这样：
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
此命令会将 **log\_min\_messages** 配置重置为默认值 **WARNING**。 有关服务器配置和允许值的详细信息，请参阅有关[服务器配置](https://www.postgresql.org/docs/9.6/static/runtime-config.html)的 PostgreSQL 文档。

## <a name="next-steps"></a>后续步骤
- 若要配置和访问服务器日志，请参阅 [Azure Database for PostgreSQL 中的服务器日志](concepts-server-logs.md)

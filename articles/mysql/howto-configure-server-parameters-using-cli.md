---
title: 在 Azure Database for MySQL 中配置服务参数
description: 本文介绍如何使用 Azure CLI 命令行实用工具在 Azure Database for MySQL 中配置服务参数。
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 4c04cb77513ec070edce739aa0a49447dc915a1b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265208"
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

## <a name="next-steps"></a>后续步骤
- 如何配置 [Azure 门户中的服务器参数](howto-server-parameters.md)
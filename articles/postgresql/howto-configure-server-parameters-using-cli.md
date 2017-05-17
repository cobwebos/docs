---
title: "在 Azure Database for PostgreSQL 中配置服务参数 | Microsoft Docs"
description: "介绍如何在 Azure Database for PostgreSQL 中配置服务参数。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e46e3d588782ec0f2ef89c58001e997b8fc2910
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>使用 Azure CLI 自定义服务器配置参数
可以使用命令行接口 (Azure CLI) 列出、显示和更新 Azure PostgreSQL 服务器的配置参数。 但是，服务器级别仅会公开引擎配置的一个子集，并且可以进行修改。 

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- 服务器和数据库 [创建 Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- 已安装 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 命令行实用工具

## <a name="list-server-configuration-parameters-for-azure-postgresql-server"></a>列出 Azure PostgreSQL 服务器的服务器配置参数
若要获取 Azure PostgreSQL 服务器中所有可修改参数及其值，请运行以下 az postgres server configuration 命令，如下所示：
> az postgres server configuration list --resource-group <resource group name> --server <server name>

例如，可以列出资源组 myresourcegroup 下的 Azure PostgreSQL 服务器 mypgserver.postgres.database.azure.com 的服务器配置参数。
```azurecli
az postgres server configuration list --resource-group myresourcegroup --server mypgserver
```
## <a name="show-server-configuration-parameter-details"></a>显示服务器配置参数详细信息
若要显示 Azure PostgreSQL 服务器的某个特定配置参数的详细信息，请运行 az postgres server configuration 命令。
```azurecli
az postgres server configuration show --name <configuration name>
   --resource-group <resource group name> --server <server name>
```
例如，可以显示资源组 **myresourcegroup 下的 Azure PostgreSQL 服务器 mypgserver.postgres.database.azure.com 的 log\_min\_messages 服务器配置参数的详细信息。 **
```azurecli
az postgres server configuration show --name log\_min\_messages --resource-group myresourcegroup --server mypgserver
```
## <a name="modify-server-configuration-parameter-value"></a>修改服务器配置参数值
可以修改 Azure PostgreSQL 服务器的某些配置参数值，这会更新 PostgreSQL 服务器引擎的基础配置值。 若要更新配置值，请运行以下 az postgres server configuration 命令。 
```azurecli
az postgres server configuration update --name <configuration name>
   --resource-group <resource group name> --server <server name>[--value]
```
例如，可以更新资源组 **myresourcegroup 下的 Azure PostgreSQL 服务器 mypgserver.postgres.database.azure.com 的 log\_min\_messages 服务器配置参数。 **
```azurecli
az postgres server configuration show --name log\_min\_messages 
   --resource-group myresourcegroup --server mypgserver --value INFO
```
若要重置配置参数的值，只需选择省略可选的值参数，服务将应用默认值。 以上述示例为例，它类似于下面这样：
```azurecli
az postgres server configuration show --name log\_min\_messages
   --resource-group myresourcegroup --server mypgserver
```
这会将 log\_min\_messages 配置重置为默认值 WARNING。 有关服务器配置和允许的值更详细的信息，请参阅有关[服务器配置](https://www.postgresql.org/docs/9.6/static/runtime-config.html)的 PostgreSQL 文档。

## <a name="next-steps"></a>后续步骤
- 若要配置和访问服务器日志，请参阅 [Azure Database for PostgreSQL 中的服务器日志](concepts-server-logs.md)

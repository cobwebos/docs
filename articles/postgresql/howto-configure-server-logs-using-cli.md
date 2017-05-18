---
title: "使用 Azure CLI 配置和访问 PostgreSQL 的服务器日志 | Microsoft Docs"
description: "介绍如何在 Azure Database for PostgreSQL 中配置和访问服务器日志。"
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
ms.openlocfilehash: dc047e6d5194035586931b3cacc4d5cd07f6cfc2
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>使用 Azure CLI 配置和访问服务器日志
可以使用命令行接口 (Azure CLI) 列出和下载 Azure PostgreSQL 服务器错误日志。 但不支持访问事务日志。 

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-azure-cli.md)
- 已安装 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 命令行实用工具

## <a name="configure-logging-for-azure-database-for-postgresql"></a>为 Azure Database for PostgreSQL 配置日志记录
可以将服务器配置为访问查询日志和错误日志。 错误日志包含自动清空、连接和检查点等信息。
1. 启用日志
2. 更新 log\_statement 和 log\_min\_duration\_statement 以启用查询日志
3. 更新保留期

请参阅[自定义服务器配置参数](howto-configure-server-parameters-using-cli.md)，了解详细信息。

## <a name="list-logs-for-azure-postgresql-server"></a>列出 Azure PostgreSQL 服务器的日志
若要列出服务器的可用日志文件，请运行az postgres server-logs 命令，如以下示例所示：
```azurecli
az postgres server-logs list --resource-group <resource group name> --server <server name> [ --file-last-written --filename-contains --max-file-size ]
```
例如，可以列出资源组 myresourcegroup 下的 Azure PostgreSQL 服务器 mypgserver.postgres.database.azure.com 的日志文件，并将其定向到名为 **log\_files\_list.txt 的文本文件。 **
```azurecli
az postgres server-logs list --resource-group **myresourcegroup** --server **mypgserver** > log\_files\_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>从服务器将日志下载到本地
还可下载 Azure PostgreSQL 服务器的单个日志文件。 
```azurecli
az postgres server-logs download –name <log file name> --resource-group <resource group name> --server <server name>
```
此示例演示将资源组 myresourcegroup 下的 Azure PostgreSQL 服务器 mypgserver.postgres.database.azure.com 的特定日志文件下载到本地环境。
```azurecli
az postgres server-logs download --name 20170414-mypgserver-postgresql.log --resource-group **myresourcegroup** --server **mypgserver**
```
## <a name="next-steps"></a>后续步骤
- 若要了解有关服务器日志的详细信息，请参阅 [Azure Database for PostgreSQL 中的服务器日志](concepts-server-logs.md)
- 有关服务器参数的详细信息，请参阅[使用 Azure CLI 自定义服务器配置参数](howto-configure-server-parameters-using-cli.md)

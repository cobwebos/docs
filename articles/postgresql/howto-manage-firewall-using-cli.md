---
title: "使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则 | Microsoft Docs"
description: "本文介绍如何使用 Azure CLI 命令行创建和管理 Azure Database for PostgreSQL 防火墙规则。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.openlocfilehash: 6f081416dd7d78f0153b3fda21a340a8c1a70c5f
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则
使用服务器级防火墙规则，管理员可以管理特定 IP 地址或一系列 IP 地址对 Azure Database for PostgreSQL 服务器的访问权限。 使用便捷的 Azure CLI 命令，可创建、更新、删除、列出和显示防火墙规则，用于管理服务器。 有关 Azure Database for PostgreSQL 防火墙的概述，请参阅 [Azure Database for PostgreSQL Server firewall rules](concepts-firewall-rules.md)（Azure Database for PostgreSQL 服务器防火墙规则）

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for PostgreSQL 服务器和数据库](quickstart-create-server-database-azure-cli.md)
- 安装 [Azure CLI 2.0](/cli/azure/install-azure-cli) 命令行实用工具或使用浏览器中的 Azure Cloud Shell。

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>配置 Azure Database for PostgreSQL 的防火墙规则
[az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) 命令用于配置防火墙规则。

## <a name="list-firewall-rules"></a>列出防火墙规则 
若要列出服务器上的现有服务器防火墙规则，请运行 [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule#list) 命令。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
输出会列出规则（如果有），默认情况下采用 JSON 格式。 可以使用 `--output table` 开关，对输出采用更易于读取的表格格式。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>创建防火墙规则
若要在服务器上创建新的防火墙规则，请运行 [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create) 命令。 

此示例允许所有 IP 地址访问服务器 mypgserver-20170401.postgres.database.azure.com
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
若要允许单个 IP 地址进行访问，请将相同地址用作起始 IP 和结束 IP，如此示例中所示。
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "AllowSingleIpAddress" --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
成功后，命令输出会列出已创建的防火墙规则的详细信息，默认情况下采用 JSON 格式。 如果失败，输出会改为显示错误消息文本。

## <a name="update-firewall-rule"></a>更新防火墙规则 
使用 [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule#update) 命令更新服务器上的现有防火墙规则。 输入现有防火墙规则的名称，提供要更新的起始 IP 和结束 IP 属性。
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
成功后，命令输出会列出更新后的防火墙规则的详细信息，默认情况下采用 JSON 格式。 如果失败，输出会改为显示错误消息文本。
> [!NOTE]
> 如果不存在防火墙规则，更新命令将创建一个。

## <a name="show-firewall-rule-details"></a>显示防火墙规则详细信息
还可以通过运行 [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule#show) 命令显示服务器的现有防火墙规则的详细信息。
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
成功后，命令输出会列出指定的防火墙规则的详细信息，默认情况下采用 JSON 格式。 如果失败，输出会改为显示错误消息文本。

## <a name="delete-firewall-rule"></a>删除防火墙规则
若要撤销服务器中某个 IP 范围的访问，可通过执行 [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule#delete) 命令删除现有防火墙规则。 输入现有防火墙规则的名称。
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
成功后没有任何输出。 如果失败，会返回错误消息文本。

## <a name="next-steps"></a>后续步骤
- 同样，也可以在 Web 浏览器中[使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](howto-manage-firewall-using-portal.md)
- 了解有关 [Azure Database for PostgreSQL 服务器防火墙规则](concepts-firewall-rules.md)的详细信息
- 有关连接到 Azure Database for PostgreSQL 服务器的帮助，请参阅 [Azure Database for PostgreSQL 的连接库](concepts-connection-libraries.md)

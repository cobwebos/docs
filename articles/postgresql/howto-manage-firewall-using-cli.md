---
title: 使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则
description: 本文介绍如何使用 Azure CLI 命令行创建和管理 Azure Database for PostgreSQL 防火墙规则。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 9a96361d3fb155ea5b400990690e3c2b1f65f819
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422366"
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则
服务器级防火墙规则可以用于管理 Azure Database for PostgreSQL 服务器从访问特定的 IP 地址或 IP 地址范围。 使用便捷的 Azure CLI 命令，可创建、更新、删除、列出和显示防火墙规则，用于管理服务器。 Azure database for PostgreSQL 防火墙规则概述，请参阅[Azure Database for PostgreSQL 服务器防火墙规则](concepts-firewall-rules.md)。

此外可以使用虚拟网络 (VNet) 规则来保护对你的服务器访问。 详细了解如何[创建和管理虚拟网络服务终结点和规则使用 Azure CLI](howto-manage-vnet-using-cli.md)。

## <a name="prerequisites"></a>必备组件
若要逐步执行本操作方法指南，需要：
- 安装 [Azure CLI](/cli/azure/install-azure-cli) 命令行实用工具或在浏览器中使用 Azure Cloud Shell。
- [Azure Database for PostgreSQL 服务器和数据库](quickstart-create-server-database-azure-cli.md)。

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>配置 Azure Database for PostgreSQL 的防火墙规则
[az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) 命令用于配置防火墙规则。

## <a name="list-firewall-rules"></a>列出防火墙规则 
若要列出现有服务器防火墙规则，请运行 [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule) 命令。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
输出将列出防火墙规则（如果有），默认情况下采用 JSON 格式。 可以使用 `--output table` 开关，对输出采用更易于读取的表格格式。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>创建防火墙规则
若要在服务器上创建新的防火墙规则，请运行 [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) 命令。 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
若要允许应用程序从 Azure IP 地址连接到 Azure Database for PostgreSQL 服务器，请提供 IP 地址 0.0.0.0 作为起始 IP 和结束 IP，如此示例所示。
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> 该选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问权限限制为仅已授权用户使用。
> 

成功后，命令输出会列出已创建的防火墙规则的详细信息，默认情况下采用 JSON 格式。 如果失败，输出会改为显示错误消息。

## <a name="update-firewall-rule"></a>更新防火墙规则 
使用 [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule) 命令更新服务器上的现有防火墙规则。 输入现有防火墙规则的名称，提供要更新的起始 IP 和结束 IP 属性。
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
成功后，命令输出会列出更新后的防火墙规则的详细信息，默认情况下采用 JSON 格式。 如果失败，输出会改为显示错误消息。
> [!NOTE]
> 如果不存在防火墙规则，更新命令将创建一个。

## <a name="show-firewall-rule-details"></a>显示防火墙规则详细信息
还可以通过运行 [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule) 命令显示现有服务器级防火墙规则的详细信息。
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
成功后，命令输出会列出指定的防火墙规则的详细信息，默认情况下采用 JSON 格式。 如果失败，输出会改为显示错误消息。

## <a name="delete-firewall-rule"></a>删除防火墙规则
若要撤销对服务器的某个 IP 范围的访问，可通过执行 [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule) 命令删除现有防火墙规则。 输入现有防火墙规则的名称。
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
成功后没有任何输出。 如果失败，会返回错误消息文本。

## <a name="next-steps"></a>后续步骤
- 同样，也可以在 Web 浏览器中[使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](howto-manage-firewall-using-portal.md)。
- 了解有关 [Azure Database for PostgreSQL 服务器防火墙规则](concepts-firewall-rules.md)的详细信息。
- 进一步保护对你的服务器的访问[创建和管理虚拟网络服务终结点和规则使用 Azure CLI](howto-manage-vnet-using-cli.md)。
- 有关连接到 Azure Database for PostgreSQL 服务器的帮助，请参阅 [Azure Database for PostgreSQL 的连接库](concepts-connection-libraries.md)。

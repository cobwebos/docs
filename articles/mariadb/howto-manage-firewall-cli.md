---
title: 使用 Azure CLI 创建和管理 Azure Database for MariaDB 防火墙规则
description: 本文介绍如何使用 Azure CLI 命令行创建和管理 Azure Database for MariaDB 防火墙规则。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 562987b953f0a8a20a917e208f43557bd768c0a0
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471148"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>使用 Azure CLI 创建和管理 Azure Database for MariaDB 防火墙规则
服务器级防火墙规则可以用于管理访问权限对 Azure Database for MariaDB 服务器从特定 IP 地址或某个范围内的 IP 地址。 使用便捷的 Azure CLI 命令，可创建、更新、删除、列出和显示防火墙规则，用于管理服务器。 MariaDB 防火墙 Azure 数据库的概述，请参阅[Azure Database for MariaDB 服务器防火墙规则](./concepts-firewall-rules.md)。

此外可以使用虚拟网络 (VNet) 规则来保护对你的服务器访问。 详细了解如何[创建和管理虚拟网络服务终结点和规则使用 Azure CLI](howto-manage-vnet-cli.md)。

## <a name="prerequisites"></a>必备组件
* [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。
* [Azure Database for MariaDB 服务器和数据库](quickstart-create-mariadb-server-database-using-azure-cli.md)。

## <a name="firewall-rule-commands"></a>防火墙规则命令：
在 Azure CLI 中使用 **az mariadb server firewall-rule** 命令创建、删除、列出、显示和更新防火墙规则。

命令：
- **create**：创建 Azure MariaDB 服务器防火墙规则。
- **delete**：删除 Azure MariaDB 服务器防火墙规则。
- **list**：列出 Azure MariaDB 服务器防火墙规则。
- **show**：显示 Azure MariaDB 服务器防火墙规则的详细信息。
- **update**：更新 Azure MariaDB 服务器防火墙规则。

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>登录到 Azure 和 MariaDB 服务器列出你的 Azure 数据库
使用 az login 命令通过 Azure 帐户安全连接到 Azure CLI。

1. 从命令行运行以下命令：
   ```azurecli
   az login
   ```
   此命令将输出要在下一步骤中使用的代码。

2. 使用 Web 浏览器打开页面 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，再输入代码。

3. 在提示符下，在使用 Azure 凭据登录。

4. 获得登录授权后，控制台中会打印出订阅列表。 复制所需订阅的 ID，便于设置要使用的当前订阅。 使用 [az account set](/cli/azure/account#az-account-set) 命令。
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. 如果不确定其名称，请列出订阅和资源组的 Azure Databases for MariaDB 服务器。 使用 [az mariadb server list](/cli/azure/mariadb/server#az-mariadb-server-list) 命令。

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   请注意列表中的名称属性，需要该属性来指定要使用的 MariaDB 服务器。 如果需要，请确认该服务器的详细信息，并使用名称属性来确保其正确。 使用 [az mariadb server show](/cli/azure/mariadb/server#az-mariadb-server-show) 命令。

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>列出 Azure Database for MariaDB 服务器上的防火墙规则 
使用服务器名称和资源组名称，列出服务器上现有的服务器防火墙规则。 使用 [az mariadb server firewall list](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) 命令。  请注意，应在“--server”开关（而不是在“--name”开关）中指定服务器名称属性。 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
输出会默认采用 JSON 格式列出规则（如果有）。 可使用“--output table”开关，以更具可读性的表格格式输出结果。
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>针对 Azure Database for MariaDB 服务器创建防火墙规则
使用 Azure MariaDB 服务器名称和资源组名称，在服务器上创建新的防火墙规则。 使用 [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) 命令。 提供规则名称以及规则的起始 IP 和结束 IP（对一系列 IP 地址提供访问权限）。
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

若要允许单个 IP 地址进行访问，请将相同的 IP 地址用作起始 IP 和结束 IP，如此示例中所示。
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

若要允许应用程序从 Azure IP 地址连接到 Azure Database for MariaDB 服务器，请提供 IP 地址 0.0.0.0 作为起始 IP 和结束 IP，如此示例所示。
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> 该选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问权限限制为仅已授权用户使用。
> 

成功后，每个 create 命令输出会列出已创建的防火墙规则的详细信息，默认采用 JSON 格式。 如果失败，输出会改为显示错误消息文本。

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>更新 Azure Database for MariaDB 服务器的防火墙规则 
使用 Azure MariaDB 服务器名称和资源组名称，更新服务器上已有的防火墙规则。 使用 [az mariadb server firewall update](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) 命令。 输入现有防火墙规则的名称，并提供要更新的起始 IP 和结束 IP 属性。
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
成功后，命令输出会列出更新后的防火墙规则的详细信息，默认采用 JSON 格式。 如果失败，输出会改为显示错误消息文本。

> [!NOTE]
> 如果不存在防火墙规则，更新命令将创建规则。

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>显示 Azure Database for MariaDB 服务器的防火墙规则详细信息
使用 Azure MariaDB 服务器名称和资源组名称，显示服务器上已有的防火墙规则的详细信息。 使用 [az mariadb server firewall show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) 命令。 输入现有防火墙规则的名称。
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
成功后，命令输出会列出指定的防火墙规则的详细信息，默认采用 JSON 格式。 如果失败，输出会改为显示错误消息文本。

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>删除 Azure Database for MariaDB 服务器的防火墙规则
使用 Azure MariaDB 服务器名称和资源组名称，从服务器中删除已有的防火墙规则。 使用 [az mariadb server firewall delete](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) 命令。 输入现有防火墙规则的名称。
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
成功后没有任何输出。 如果失败，会显示错误消息文本。

## <a name="next-steps"></a>后续步骤
- 了解有关 [Azure Database for MariaDB 服务器防火墙规则](./concepts-firewall-rules.md)的详细信息。
- [使用 Azure 门户创建和管理 Azure Database for MariaDB 防火墙规则](./howto-manage-firewall-portal.md)。
- 进一步保护对你的服务器的访问[创建和管理虚拟网络服务终结点和规则使用 Azure CLI](howto-manage-vnet-cli.md)。

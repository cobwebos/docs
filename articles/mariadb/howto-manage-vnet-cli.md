---
title: 使用 Azure CLI 创建和管理 Azure Database for MariaDB VNet 服务终结点和规则 | Microsoft Docs
description: 本文介绍如何使用 Azure CLI 命令行创建和管理 Azure Database for MariaDB VNet 服务终结点和规则。
services: mariaDB
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/20/2018
ms.openlocfilehash: 7f39eb2b3020016d1fb14e8c611ae00dfab07c11
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336324"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>使用 Azure CLI 创建和管理 Azure Database for MariaDB VNet 服务终结点

> [!IMPORTANT]
> VNet 服务终结点功能目前为公共预览版。

虚拟网络 (VNet) 服务终结点和规则将虚拟网络的专用地址空间扩展到 Azure Database for MariaDB 服务器。 使用便捷的 Azure 命令行接口 (CLI) 命令，可创建、更新、删除、列出和显示 VNet 服务终结点和规则，用于管理服务器。 若要概览 Azure Database for MariaDB VNet 服务终结点（包括限制），请参阅 [Azure Database for MariaDB 服务器 VNet 服务终结点](concepts-data-access-security-vnet.md)。 在 Azure Database for MariaDB 的所有支持区域中，VNet 服务终结点均可用。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- 安装 [Azure CLI](/cli/azure/install-azure-cli)，或在浏览器中使用 Azure Cloud Shell。
- [Azure Database for MariaDB 服务器和数据库](quickstart-create-mariadb-server-database-using-azure-cli.md)。

> [!NOTE]
> 只有常规用途和内存优化服务器才支持 VNet 服务终结点。

## <a name="configure-vnet-service-endpoints"></a>配置 VNet 服务终结点
[az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) 命令用于配置虚拟网络。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

如果在本地运行 CLI，需要使用 [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) 命令登录帐户。 记下与订阅名称相对应的命令输出中的 **id** 属性。
```azurecli-interactive
az login
```

如果有多个订阅，请选择应计费的资源所在的相应订阅。 使用 [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) 命令选择帐户下的特定订阅 ID。 用订阅的 **az login** 输出中的 **id** 属性代替订阅 id 占位符。

- 该帐户必须拥有创建虚拟网络和服务终结点所需的必要权限。

对虚拟网络拥有写入访问权限的用户可在虚拟网络上单独配置服务终结点。

若要在 VNet 中保护 Azure 服务资源，用户必须对所添加的子网拥有“Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/”权限。 此权限默认包含在内置的服务管理员角色中，可以通过创建自定义角色进行修改。

详细了解[内置角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)以及将特定的权限分配到[自定义角色](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)。

VNet 和 Azure 服务资源可以位于相同或不同的订阅中。 如果 VNet 和 Azure 服务资源位于不同的订阅中，资源应在相同的 Active Directory (AD) 租户下。

> [!IMPORTANT]
> 强烈建议在配置服务终结点前，先阅读本文介绍的服务终结点配置和注意事项。 **虚拟网络服务终结点**：[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)是一个子网，其属性值包括一个或多个正式的 Azure 服务类型名称。 VNet 服务终结点使用服务类型名称 Microsoft.Sql，可引用名为“SQL 数据库”的 Azure 服务。 此服务标记也适用于 Azure SQL 数据库、Azure Database for MariaDB、PostgreSQL 和 MySQL 服务。 请务必要注意，对 VNet 服务终结点应用 Microsoft.Sql 服务标记时，它会为所有 Azure 数据库服务配置服务终结点流量，其中包括 Azure SQL 数据库、Azure Database for PostgreSQL、Azure Database for MariaDB 和子网上的 Azure Database for MySQL 服务器。

### <a name="sample-script"></a>示例脚本

此示例脚本用于创建 Azure Database for MariaDB 服务器、创建 VNet 和 VNet 服务终结点，以及通过 VNet 规则在子网中保护服务器。 在此示例脚本中，更改管理员用户名和密码。 将 `az account set --subscription` 命令中使用的 SubscriptionID 替换为你自己的订阅标识符。

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->